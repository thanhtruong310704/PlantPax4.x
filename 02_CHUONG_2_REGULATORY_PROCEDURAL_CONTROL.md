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

---
# PHẦN TRA CỨU CHI TIẾT TOÀN BỘ BIẾN (INPUT, OUTPUT, LOCAL TAGS), CÁCH SỬ DỤNG & CẤU HÌNH TỪNG KHỐI AOI

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PIDE`
**Thuộc chương:** [Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)](../02_CHUONG_2_REGULATORY_PROCEDURAL_CONTROL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 79–83)  
**Tệp định nghĩa khối lệnh:** `P_PIDE_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_PIDE`:** **218** Biến đầu vào (`Input Parameters`) | **149** Biến đầu ra (`Output Parameters`) | **59** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_PIDE` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển vòng kín **PID nâng cao** chuẩn PlantPAx (bao bọc lệnh `PIDE` của Logix). Hỗ trợ điều khiển Đơn vòng, Tầng (**Cascade**), Tỷ lệ (**Ratio**), Bù nhiễu tiến cử (**Feedforward**), Tự động dò thông số (**Autotune**), 4 cảnh báo sai lệch (`HiHiDev`..`LoLoDev`), Setpoint Ramping và cưỡng bức CV về giá trị an toàn khi mất liên động.
- **Khi nào CẦN sử dụng:** Cho **mọi bài toán điều khiển liên tục (Continuous Control)** cần giữ ổn định Nhiệt độ, Áp suất, Lưu lượng, Mức bồn, pH, Oxy hòa tan bằng van tuyến tính hoặc biến tần.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi điều khiển đóng/ngắt 2 vị trí theo khoảng trễ (như bơm hố thu) → Dùng [`P_DBC.md`](./P_DBC.md).

### Quy trình 4 bước đấu nối và cấu hình `P_PIDE`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_PV`, `Inp_CascSP`, `Inp_FF`, `Inp_FFPrev`, `Inp_CVInitVal`, `Inp_CVPrev`, `Inp_UseFFPrev`, `Inp_UseCVInitVal`, `Inp_UseCVPrev`, `Inp_WindupHi`, `Inp_WindupLo`, `Inp_PVBad`, `Inp_PVUncertain`, `Inp_CVIOFault`, `Inp_PVSrcQ`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasMoreObj`, `Cfg_HasRatio`, `Cfg_HasCasc`, `Cfg_HasAuto`, `Cfg_HasMan`, `Cfg_HasSPRamp`, `Cfg_LockLM`, `Cfg_PVTrack`, `Cfg_CtrlAction`, `Cfg_Depend`, `Cfg_DerivSmooth`, `Cfg_ZCOff`, `Cfg_PropSPAct`, `Cfg_DerivSPAct`, `Cfg_LimitManCV`, `Cfg_InitToMan`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_CascSPNavTag`, `Cfg_CVEU`, `Cfg_CVNavTag`, `Cfg_Desc`, `Cfg_Label`, `Cfg_PVEU`, `Cfg_PVNavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_PIDE` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_PIDE` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_Ratio`, `PSet_SP`, `PSet_SPRoCLimInc`, `PSet_SPRoCLimDec`, `PSet_SPTarget`, `PSet_SPRampT`, `PSet_CV`, `PSet_Owner`, `PCmd_Casc`, `PCmd_Auto`, `PCmd_Man`, `PCmd_NormLM`, `PCmd_SPRampStart`, `PCmd_SPRampStop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_CV`, `Val_PV`, `Val_Ratio`, `Val_SPSet`, `Val_SP`, `Val_SPTarget`, `Val_SPRampT`, `Val_SPRampRoC`, `Val_SPRoCInc`, `Val_SPRoCDec`, `Val_E`, `Val_CVSet`, `Val_CVOut`, `Val_PVPercent`, `Val_SPPercent`, `Val_EPercent`, `Val_CVPercent`, `Val_ActExecT`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 218 Biến Đầu Vào (`Input Parameters`) của `P_PIDE`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_PV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào (Process Variable - PV) đọc từ cảm biến hoặc thẻ Analog/Discrete Input |
| **`Inp_CascSP`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị đặt tầng (Cascade Setpoint) từ bộ điều khiển sơ cấp hoặc biến độc lập trong điều khiển Tỷ lệ (Ratio, đơn vị PVEU) |
| **`Inp_FF`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu bù nhiễu tiến cử đầu vào (Feedforward Term, tính theo đơn vị CVEU) |
| **`Inp_FFPrev`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Giá trị phản hồi thành phần bù nhiễu tiến cử (FF Feedback, đơn vị CVEU) từ khối phía sau |
| **`Inp_CVInitVal`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Giá trị khởi tạo dành cho ngõ ra CV (đơn vị CVEU) khi có yêu cầu khởi tạo bám trạng thái (Inp_UseCVInitVal = 1) |
| **`Inp_CVPrev`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Giá trị phản hồi CV thực tế từ khối phía sau (Downstream CV Feedback, đơn vị CVEU) dùng để tính toán chống bão hòa tích phân |
| **`Inp_UseFFPrev`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Sử dụng giá trị Inp_FFPrev làm thành phần FF chu kỳ trước; 0 = Dùng giá trị của chu kỳ quét trước |
| **`Inp_UseCVInitVal`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Cờ yêu cầu khởi tạo ngõ ra CV bám theo giá trị Inp_CVInitVal (1 = Khởi tạo bám theo thiết bị phía sau để chuyển mạch không giật) |
| **`Inp_UseCVPrev`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Sử dụng giá trị phản hồi Inp_CVPrev làm CV chu kỳ trước của thuật toán PID; 0 = Dùng giá trị tính toán của chu kỳ quét trước |
| **`Inp_WindupHi`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Cờ báo bão hòa giới hạn trên từ khối phía sau (1 = Ngăn bộ điều khiển sơ cấp tiếp tục tăng ngõ ra CV) |
| **`Inp_WindupLo`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Cờ báo bão hòa giới hạn dưới từ khối phía sau (1 = Ngăn bộ điều khiển sơ cấp tiếp tục giảm ngõ ra CV) |
| **`Inp_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc lỗi truyền thông đầu vào PV (1 = Lỗi/Bad, 0 = Tốt/OK — thường nối từ bit Channel Fault của thẻ I/O) |
| **`Inp_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu đầu vào PV không chắc chắn (1 = Không chắc chắn/Uncertain, 0 = Tốt/OK) |
| **`Inp_CVIOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông của kênh đầu ra tương tự CV (1 = Lỗi truyền thông CV I/O, 0 = Tốt/OK) |
| **`Inp_PVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu PV đầu vào (kiểu liệt kê enum, nối từ chân SrcQ của khối kênh P_AIChan nếu có) |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và trạng thái xác nhận (enum) của đối tượng PV liên kết |
| **`Inp_CVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng cơ cấu chấp hành CV liên kết phía sau |
| **`Inp_CascSPNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng cấp tín hiệu Cascade SP |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_HandFdbk`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Giá trị phản hồi vị trí thực tế của cơ cấu chấp hành khi ở chế độ Bằng tay tại chỗ (Hand/Tieback Feedback, đơn vị CVEU) |
| **`Inp_HandFdbkBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng hoặc mất truyền thông của tín hiệu phản hồi Inp_HandFdbk (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh chọn chế độ vòng PID khi ở chế độ Cưỡng bức (Override): 0 = Không đổi chế độ, 1 = Chuyển sang Bằng tay (Manual), 2 = Chuyển sang Tự động (Auto), 3 = Chuyển sang Tầng/Tỷ lệ (Cascade/Ratio), 4 = Chuyển về Mặc định (Normal) |
| **`Inp_OvrdRatio`** | `REAL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Hệ số tỷ lệ (Ratio) khi vòng điều khiển ở chế độ Cưỡng bức (Override Ratio) |
| **`Inp_OvrdSP`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Giá trị đặt Setpoint (SP) khi vòng điều khiển ở chế độ Cưỡng bức (Override SP, đơn vị PVEU) |
| **`Inp_OvrdCV`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Giá trị đặt biến điều khiển CV khi ở chế độ Cưỡng bức (Override CV Target, đơn vị kỹ thuật EU) |
| **`Inp_OvrdSPTarget`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Giá trị đích cuối cùng của quá trình Ramp SP khi ở chế độ Cưỡng bức (đơn vị PVEU) |
| **`Inp_OvrdSPRampT`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Thời gian tăng/giảm SP theo độ dốc khi ở chế độ Cưỡng bức (0.0 đến 28800.0 giây) |
| **`Inp_OvrdSPRoCLimInc`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Giới hạn tốc độ tăng SP tối đa khi ở chế độ Cưỡng bức (PVEU/giây) |
| **`Inp_OvrdSPRoCLimDec`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Giới hạn tốc độ giảm SP tối đa khi ở chế độ Cưỡng bức (PVEU/giây) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HiHiDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát độ lệch Rất Cao (High-High Deviation): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_HiDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát độ lệch Cao (High Deviation): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát độ lệch Thấp (Low Deviation): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoLoDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát độ lệch Rất Thấp (Low-Low Deviation): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasRatio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Chế độ hoạt động khi chọn Cascade: 1 = Điều khiển theo Tỷ lệ (Ratio Control, nhân Inp_CascSP với hệ số Ratio); 0 = Điều khiển Tầng chuẩn (Cascade Control) |
| **`Cfg_HasCasc`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép chuyển vòng điều khiển sang chế độ Điều khiển tầng / Tỷ lệ (Cascade / Ratio Mode) |
| **`Cfg_HasAuto`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép chuyển vòng điều khiển sang chế độ Tự động (Automatic Mode) |
| **`Cfg_HasMan`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép chuyển vòng điều khiển sang chế độ Bằng tay (Manual Mode) |
| **`Cfg_HasSPRamp`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bật công cụ thay đổi giá trị đặt SP theo độ dốc (Setpoint Ramp Wizard) trên HMI |
| **`Cfg_LockLM`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khóa cứng vòng điều khiển ở chế độ Mặc định chuẩn (Cfg_NormLM); 0 = Cho phép chuyển đổi chế độ linh hoạt |
| **`Cfg_PVTrack`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép giá trị đặt SP tự động bám theo PV thực tế (SP Tracks PV) khi vòng PID ở chế độ Bằng tay (Manual); 0 = Giữ nguyên SP |
| **`Cfg_CtrlAction`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Chiều tác động điều khiển của bộ PID: 1 = Tác động Thuận (Direct Acting: E = PV - SP, PV tăng thì CV tăng — ví dụ làm mát/xả áp); 0 = Tác động Nghịch (Reverse Acting: E = SP - PV, PV giảm thì CV tăng — ví dụ gia nhiệt/cấp áp) |
| **`Cfg_Depend`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Kiểu phương trình thuật toán PID: 1 = Hệ số khuếch đại Phụ thuộc (Dependent Gains — ISA Standard); 0 = Hệ số khuếch đại Độc lập (Independent Gains) |
| **`Cfg_DerivSmooth`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bật bộ lọc làm mịn thành phần Vi phân (Derivative Smoothing) để giảm nhiễu tần số cao |
| **`Cfg_ZCOff`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chỉ cần sai lệch nằm trong vùng chết là ngừng chỉnh CV (không bắt buộc sai lệch phải đổi dấu đi qua điểm 0); 0 = Sai lệch phải đi qua điểm 0 mới kích hoạt vùng chết |
| **`Cfg_PropSPAct`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cách tính thành phần Tỷ lệ (P): 0 = Tính trên độ biến thiên của PV (chống vọt lố CV khi thay đổi SP đột ngột); 1 = Tính trên sai lệch E = SP - PV |
| **`Cfg_DerivSPAct`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cách tính thành phần Vi phân (D): 0 = Tính trên độ biến thiên của PV (tránh xung đạo hàm vọt lố khi đổi SP); 1 = Tính trên sai lệch E = SP - PV |
| **`Cfg_LimitManCV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Áp dụng kẹp giới hạn Cfg_MinCV..Cfg_MaxCV ngay cả khi điều khiển bằng tay (Manual); 0 = Không giới hạn CV ở chế độ Manual |
| **`Cfg_InitToMan`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động chuyển vòng điều khiển về chế độ Manual khi nhận yêu cầu khởi tạo; 0 = Giữ nguyên chế độ |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép giá trị đặt của Program và Operator tự động bám theo giá trị CV khi đang ở chế độ Override hoặc Hand |
| **`Cfg_SkipSPRoCLim`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bỏ qua bộ giới hạn tốc độ thay đổi SP khi ở chế độ Bảo trì, Cưỡng bức (Override) hoặc khi ngắt Liên động |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasCascSPNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép hiển thị nút điều hướng nhảy tới Faceplate của khối cấp tín hiệu Cascade SP trên HMI |
| **`Cfg_HasPVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép hiển thị nút điều hướng nhảy tới Faceplate của khối cảm biến PV trên HMI |
| **`Cfg_HasCVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép hiển thị nút điều hướng nhảy tới Faceplate của khối cơ cấu chấp hành CV trên HMI |
| **`Cfg_OvrdIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override được quyền bỏ qua các điều kiện Khóa liên động (Interlocks) có thể bypass; 0 = Luôn kiểm tra Interlocks |
| **`Cfg_IntlkTripLatch`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự chốt giữ trạng thái ngắt liên động của vòng PID cho đến khi nhấn Reset; 0 = Tự phục hồi ngay khi liên động OK trở lại |
| **`Cfg_SPFailLatch`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự chốt giữ trạng thái bảo vệ khi lỗi Cascade SP cho đến khi nhấn Reset; 0 = Tự phục hồi khi SP tốt trở lại |
| **`Cfg_PVFailLatch`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự chốt giữ (Latch) trạng thái bảo vệ khi lỗi PV cho đến khi nhấn Reset; 0 = Tự phục hồi ngay khi PV tốt trở lại |
| **`Cfg_CVFailLatch`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự chốt giữ trạng thái bảo vệ khi lỗi kênh CV cho đến khi nhấn Reset; 0 = Tự phục hồi khi hết lỗi |
| **`Cfg_NormLM`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Chế độ vòng điều khiển chuẩn mặc định (Normal Loop Mode): 0 = Vô hiệu hóa, 1 = Manual, 2 = Auto, 3 = Cascade |
| **`Cfg_PwrupLM`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Chế độ vòng điều khiển khi vừa bật nguồn PLC (Powerup Loop Mode): 0 = Giữ chế độ trước khi mất điện, 1 = Manual (với Cfg_PwrupCV), 2 = Auto (với Cfg_PwrupSP), 3 = Cascade, 4 = Normal Mode |
| **`Cfg_IntlkTripSPAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý SP khi vi phạm Khóa liên động: 0 = Giữ nguyên, 1 = Giữ SP tốt cuối cùng, 2 = Đưa SP về Cfg_IntlkSP, 3 = Đặt SP bằng PV hiện tại |
| **`Cfg_SPFailSPAct`** | `DINT` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý SP khi tín hiệu Cascade SP bị lỗi: 1 = Giữ SP tốt cuối cùng, 2 = Đưa SP về Cfg_IntlkSP, 3 = Đặt SP bằng giá trị PV hiện tại |
| **`Cfg_PVFailSPAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý giá trị đặt SP khi cảm biến PV bị lỗi: 0 = Giữ nguyên SP, 1 = Giữ SP tốt cuối cùng, 2 = Đưa SP về Cfg_IntlkSP |
| **`Cfg_CVFailSPAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý SP khi kênh xuất CV bị lỗi: 0 = Giữ nguyên, 1 = Giữ SP tốt cuối cùng, 2 = Đưa SP về Cfg_IntlkSP, 3 = Đặt SP bằng PV hiện tại |
| **`Cfg_IntlkTripCVAct`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra CV khi vi phạm Khóa liên động: 0 = Không can thiệp, 1 = Giữ CV tốt cuối cùng, 2 = Đưa CV về giá trị an toàn Cfg_IntlkCV |
| **`Cfg_SPFailCVAct`** | `DINT` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra CV khi tín hiệu Cascade SP bị lỗi: 0 = Không can thiệp, 1 = Giữ CV tốt cuối cùng, 2 = Đưa CV về Cfg_IntlkCV |
| **`Cfg_PVFailCVAct`** | `DINT` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra CV khi cảm biến PV bị lỗi: 1 = Giữ nguyên giá trị CV tốt cuối cùng (Hold Last Good), 2 = Đưa CV về giá trị an toàn Cfg_IntlkCV |
| **`Cfg_CVFailCVAct`** | `DINT` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra CV khi kênh xuất CV bị lỗi: 1 = Giữ CV tốt cuối cùng, 2 = Đưa CV về Cfg_IntlkCV |
| **`Cfg_IntlkTripLMAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động chuyển chế độ vòng PID khi vi phạm Khóa liên động (Interlock Trip): 0 = Giữ nguyên, 1 = Ép về Manual, 2 = Chuyển về Auto hoặc Manual |
| **`Cfg_SPFailLMAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động chuyển chế độ vòng PID khi tín hiệu Cascade SP bị lỗi: 0 = Giữ nguyên, 1 = Ép về Manual, 2 = Chuyển về Auto hoặc Manual |
| **`Cfg_PVFailLMAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động chuyển chế độ vòng PID khi cảm biến PV bị lỗi (PV Fail): 0 = Giữ nguyên chế độ, 1 = Ép về Manual, 2 = Chỉ cho phép Auto hoặc Manual (thoát Cascade) |
| **`Cfg_CVFailLMAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động chuyển chế độ vòng PID khi kênh xuất CV bị lỗi: 0 = Giữ nguyên, 1 = Ép về Manual, 2 = Chuyển về Auto hoặc Manual |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_CVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị ngõ ra CV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasHiHiDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo độ lệch Rất Cao (High-High Deviation): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasHiDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo độ lệch Cao (High Deviation): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo độ lệch Thấp (Low Deviation): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoLoDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo độ lệch Rất Thấp (Low-Low Deviation): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HiHiDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo độ lệch Rất Cao (High-High Deviation): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo độ lệch Cao (High Deviation): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo độ lệch Thấp (Low Deviation): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoLoDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo độ lệch Rất Thấp (Low-Low Deviation): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiHiDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo độ lệch Rất Cao (High-High Deviation): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo độ lệch Cao (High Deviation): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo độ lệch Thấp (Low Deviation): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoLoDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo độ lệch Rất Thấp (Low-Low Deviation): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiHiDevSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo độ lệch Rất Cao (High-High Deviation) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_HiDevSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo độ lệch Cao (High Deviation) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoDevSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo độ lệch Thấp (Low Deviation) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoLoDevSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo độ lệch Rất Thấp (Low-Low Deviation) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MinRatio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn dưới tối thiểu của hệ số tỷ lệ (Ratio Min) khi chạy chế độ Ratio |
| **`Cfg_MaxRatio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn trên tối đa của hệ số tỷ lệ (Ratio Max) khi chạy chế độ Ratio |
| **`Cfg_MinSP`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn dưới tối thiểu cho phép nhập của giá trị đặt Setpoint (SP Min, đơn vị PVEU) |
| **`Cfg_MaxSP`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn trên tối đa cho phép nhập của giá trị đặt Setpoint (SP Max, đơn vị PVEU) |
| **`Cfg_MaxSPRoCInc`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn trần tối đa cho tốc độ tăng SP (Maximum SP Positive Rate of Change, đơn vị PVEU/giây) |
| **`Cfg_MaxSPRoCDec`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn trần tối đa cho tốc độ giảm SP (Maximum SP Negative Rate of Change, đơn vị PVEU/giây) |
| **`Cfg_SPRampMaxDev`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch tối đa giữa PV và SP trong khi đang Ramp: nếu PV tụt lại quá ngưỡng này, quá trình Ramp SP sẽ tạm dừng chờ PV đuổi kịp (0.0 = không bao giờ tạm dừng) |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Min (PV EU Min — hỗ trợ cả quy đổi ngược nếu đặt EUMin > EUMax) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Max (PV EU Max — hỗ trợ cả quy đổi ngược nếu đặt EUMax < EUMin) |
| **`Cfg_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật đầu ra CV tương ứng với Cfg_CVRawMin (thường là 0.0%) |
| **`Cfg_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật đầu ra CV tương ứng với Cfg_CVRawMax (thường là 100.0%) |
| **`Cfg_CVRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô đầu ra tối thiểu (CV Raw Min) gửi ra thẻ Analog Output tương ứng với Cfg_CVEUMin |
| **`Cfg_CVRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô đầu ra tối đa (CV Raw Max) gửi ra thẻ Analog Output tương ứng với Cfg_CVEUMax |
| **`Cfg_ExecT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Chu kỳ thời gian lấy mẫu thực thi thuật toán PID (Execution Interval, tính bằng giây) |
| **`Cfg_PGain`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số khuếch đại Tỷ lệ (Proportional Gain Kp hoặc Loop Gain Kc, không thứ nguyên) |
| **`Cfg_IGain`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số khuếch đại Tích phân (Integral Gain Ki, đơn vị 1/phút hoặc phút/lần lặp) |
| **`Cfg_DGain`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số khuếch đại Vi phân (Derivative Gain Kd, đơn vị phút) |
| **`Cfg_ZCDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Biên độ vùng chết qua điểm 0 (Zero-Crossing Deadband, đơn vị PVEU) quanh giá trị đặt SP mà trong đó CV được giữ nguyên |
| **`Cfg_MinCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho phép của ngõ ra điều khiển CV (Minimum CV Limit, đơn vị kỹ thuật EU) |
| **`Cfg_MaxCV`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho phép của ngõ ra điều khiển CV (Maximum CV Limit, đơn vị kỹ thuật EU) |
| **`Cfg_CVRoCLim`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn tốc độ biến thiên tối đa của ngõ ra điều khiển CV (CV Rate-of-Change Limit, đơn vị CVEU/giây) |
| **`Cfg_MaxInactiveCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng CV tối thiểu (đơn vị CVEU) để bật cờ báo thiết bị đang tích cực hoạt động (Sts_Active = 1 trên HMI) |
| **`Cfg_IntlkSP`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đặt SP an toàn (đơn vị PVEU) được áp dụng khi xảy ra ngắt liên động hoặc lỗi tín hiệu |
| **`Cfg_IntlkCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị ngõ ra CV an toàn (đơn vị kỹ thuật EU) được xuất ra khi vi phạm Khóa liên động (nếu Cfg_ShedHold = 0) |
| **`Cfg_PwrupSP`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đặt SP khởi tạo khi bật nguồn PLC (đơn vị PVEU, áp dụng khi Cfg_PwrupLM = 1 hoặc 2) |
| **`Cfg_PwrupCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị ngõ ra CV khởi tạo khi bật nguồn PLC (đơn vị CVEU, áp dụng khi Cfg_PwrupLM <> 0) |
| **`Cfg_HiHiDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho độ lệch Rất Cao (High-High Deviation): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho độ lệch Cao (High Deviation): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho độ lệch Thấp (Low Deviation): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoLoDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho độ lệch Rất Thấp (Low-Low Deviation): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiHiDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho độ lệch Rất Cao (High-High Deviation): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho độ lệch Cao (High Deviation): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho độ lệch Thấp (Low Deviation): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoLoDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho độ lệch Rất Thấp (Low-Low Deviation): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiHiDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho độ lệch Rất Cao (High-High Deviation): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho độ lệch Cao (High Deviation): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho độ lệch Thấp (Low Deviation): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoLoDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho độ lệch Rất Thấp (Low-Low Deviation): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiHiDevLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho độ lệch Rất Cao (High-High Deviation) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_HiHiDevDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng độ lệch Rất Cao (High-High Deviation) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiDevLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho độ lệch Cao (High Deviation) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_HiDevDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng độ lệch Cao (High Deviation) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoDevLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho độ lệch Thấp (Low Deviation) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_LoDevDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng độ lệch Thấp (Low Deviation) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoLoDevLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho độ lệch Rất Thấp (Low-Low Deviation) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_LoLoDevDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng độ lệch Rất Thấp (Low-Low Deviation) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Operator giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Program (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Program giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Operator (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép External giữ quyền điều khiển khi ở Program/Operator (.0 = Giữ tốc độ đặt Reference, .1 = Giữ lệnh điều khiển Commands) |
| **`PSet_Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Hệ số tỷ lệ (Ratio, không thứ nguyên) khi vòng điều khiển chạy ở chế độ Cascade/Ratio |
| **`PSet_SP`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị đặt chính (Setpoint - SP, tính theo đơn vị kỹ thuật PV EU) khi chạy chế độ Tự động (Auto) |
| **`PSet_SPRoCLimInc`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giới hạn tốc độ tăng tối đa của giá trị đặt SP (Positive Rate-of-Change Limit, đơn vị PVEU/giây) |
| **`PSet_SPRoCLimDec`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giới hạn tốc độ giảm tối đa của giá trị đặt SP (Negative Rate-of-Change Limit, đơn vị PVEU/giây) |
| **`PSet_SPTarget`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị đích cuối cùng của quá trình tăng/giảm SP theo độ dốc (Setpoint Ramp Target, đơn vị PVEU) |
| **`PSet_SPRampT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Thời gian tăng/giảm giá trị đặt SP đến đích (Setpoint Ramp Time, hợp lệ từ 0.0 đến 28800.0 giây) |
| **`PSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`XSet_Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Hệ số tỷ lệ (Ratio, không thứ nguyên) khi vòng điều khiển chạy ở chế độ Cascade/Ratio |
| **`XSet_SP`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị đặt chính (Setpoint - SP, tính theo đơn vị kỹ thuật PV EU) khi chạy chế độ Tự động (Auto) |
| **`XSet_SPRoCLimInc`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giới hạn tốc độ tăng tối đa của giá trị đặt SP (Positive Rate-of-Change Limit, đơn vị PVEU/giây) |
| **`XSet_SPRoCLimDec`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giới hạn tốc độ giảm tối đa của giá trị đặt SP (Negative Rate-of-Change Limit, đơn vị PVEU/giây) |
| **`XSet_SPTarget`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị đích cuối cùng của quá trình tăng/giảm SP theo độ dốc (Setpoint Ramp Target, đơn vị PVEU) |
| **`XSet_SPRampT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Thời gian tăng/giảm giá trị đặt SP đến đích (Setpoint Ramp Time, hợp lệ từ 0.0 đến 28800.0 giây) |
| **`XSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU) |
| **`PCmd_Casc`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển vòng điều khiển sang chế độ Tầng / Tỷ lệ (Cascade / Ratio Loop Mode) |
| **`PCmd_Auto`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển vòng điều khiển sang chế độ Tự động (Automatic Loop Mode) |
| **`PCmd_Man`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển vòng điều khiển sang chế độ Bằng tay (Manual Loop Mode) |
| **`PCmd_NormLM`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển vòng điều khiển về chế độ Mặc định chuẩn (Normal Loop Mode theo Cfg_NormLM) |
| **`PCmd_SPRampStart`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Bắt đầu chạy quy trình thay đổi giá trị đặt SP theo độ dốc (Start Setpoint Ramping) |
| **`PCmd_SPRampStop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng quy trình thay đổi giá trị đặt SP theo độ dốc (Stop Setpoint Ramping) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_HiHiDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo độ lệch Rất Cao (High-High Deviation) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo độ lệch Cao (High Deviation) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo độ lệch Thấp (Low Deviation) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoLoDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo độ lệch Rất Thấp (Low-Low Deviation) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiHiDevSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo độ lệch Rất Cao (High-High Deviation) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiDevSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo độ lệch Cao (High Deviation) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoDevSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo độ lệch Thấp (Low Deviation) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoDevSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo độ lệch Rất Thấp (Low-Low Deviation) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiDevUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo độ lệch Rất Cao (High-High Deviation) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiDevUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo độ lệch Cao (High Deviation) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoDevUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo độ lệch Thấp (Low Deviation) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoDevUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo độ lệch Rất Thấp (Low-Low Deviation) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiDevUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo độ lệch Rất Cao (High-High Deviation) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_HiDevUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo độ lệch Cao (High Deviation) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoDevUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo độ lệch Thấp (Low Deviation) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoLoDevUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo độ lệch Rất Thấp (Low-Low Deviation) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_Casc`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chuyển vòng điều khiển sang chế độ Tầng / Tỷ lệ (Cascade / Ratio Loop Mode) |
| **`XCmd_Auto`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chuyển vòng điều khiển sang chế độ Tự động (Automatic Loop Mode) |
| **`XCmd_Man`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chuyển vòng điều khiển sang chế độ Bằng tay (Manual Loop Mode) |
| **`XCmd_NormLM`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chuyển vòng điều khiển về chế độ Mặc định chuẩn (Normal Loop Mode theo Cfg_NormLM) |
| **`XCmd_SPRampStart`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Bắt đầu chạy quy trình thay đổi giá trị đặt SP theo độ dốc (Start Setpoint Ramping) |
| **`XCmd_SPRampStop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng quy trình thay đổi giá trị đặt SP theo độ dốc (Stop Setpoint Ramping) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 149 Biến Đầu Ra (`Output Parameters`) của `P_PIDE`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_CV`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển tương tự (Controlled Variable - CV) gửi tới cơ cấu chấp hành / thẻ Analog Output (đơn vị Raw hoặc EU) |
| **`Val_PV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến sơ cấp kỹ thuật số (HART Primary Variable - PV, tính theo đơn vị PVEU) |
| **`Val_Ratio`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị hệ số tỷ lệ (Loop Ratio, không thứ nguyên) đang áp dụng thực tế |
| **`Val_SPSet`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đặt Setpoint (SP) được chọn từ nguồn lệnh (trước khi Ramp và kẹp giới hạn, đơn vị PVEU) |
| **`Val_SP`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đặt Setpoint (SP) đang áp dụng thực tế cho thuật toán PID (sau khi đã Ramp và kẹp giới hạn, đơn vị PVEU) |
| **`Val_SPTarget`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đích cuối cùng của quá trình tăng/giảm SP theo độ dốc (đơn vị PVEU) |
| **`Val_SPRampT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian tăng/giảm SP đến đích đang được chấp nhận (từ 0.0 đến 28800.0 giây) |
| **`Val_SPRampRoC`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tốc độ thay đổi SP tự động tính toán được từ trình thuật sĩ Setpoint Ramp Wizard (đơn vị PVEU/giây) |
| **`Val_SPRoCInc`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giới hạn tốc độ tăng SP tối đa hiện hành (đơn vị PVEU/giây) |
| **`Val_SPRoCDec`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giới hạn tốc độ giảm SP tối đa hiện hành (đơn vị PVEU/giây) |
| **`Val_E`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Sai lệch điều khiển hiện tại của vòng PID (Loop Error E = PV - SP hoặc SP - PV, đơn vị PVEU) |
| **`Val_CVSet`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đặt CV đang được chọn (trước khi qua bộ giới hạn tốc độ thay đổi RoC, đơn vị EU) |
| **`Val_CVOut`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến điều khiển CV xuất ra thực tế (sau khi qua bộ giới hạn tốc độ thay đổi RoC, đơn vị EU) |
| **`Val_PVPercent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến quá trình (PV) tính theo phần trăm toàn dải đo PV (0..100% Span) |
| **`Val_SPPercent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đặt Setpoint (SP) tính theo phần trăm toàn dải đo PV (0..100% Span) |
| **`Val_EPercent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Sai lệch điều khiển hiện tại của vòng PID tính theo phần trăm toàn dải đo (% Span) |
| **`Val_CVPercent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến điều khiển CV tính theo phần trăm toàn dải đo (0..100% Span) |
| **`Val_ActExecT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Chu kỳ thời gian thực thi thực tế của thuật toán PID (Actual Execution Interval, tính bằng giây) |
| **`Val_Init`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị khởi tạo (Initialization Value, đơn vị PVEU) gửi ngược lên vòng PID sơ cấp (Primary Loop) trong cấu trúc Cascade |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đo đơn vị kỹ thuật PV = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đo đơn vị kỹ thuật PV = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đơn vị kỹ thuật ngõ ra CV = MIN(Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đơn vị kỹ thuật ngõ ra CV = MAX(Cfg_CVEUMin, Cfg_CVEUMax) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái chế độ vòng điều khiển PID: 0 = Chưa xác định / Ngưng phục vụ, 1 = Chế độ Bằng tay (Manual), 2 = Chế độ Tự động (Auto), 3 = Chế độ Tầng (Cascade không tỷ lệ), 4 = Chế độ Tầng có Tỷ lệ (Cascade + Ratio) |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_State`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái máy trạng thái nội bộ (Internal Logic State) dùng để tạo hiệu ứng chuyển động sơ đồ trạng thái trên HMI Faceplate |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_InstrFaults1`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái lỗi nội bộ số 1 của khối lệnh PIDE chuẩn (tham khảo tài liệu trợ giúp lệnh PIDE trong Studio 5000) |
| **`Sts_InstrFaults2`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái lỗi nội bộ số 2 của khối lệnh PIDE chuẩn (tham khảo tài liệu trợ giúp lệnh PIDE trong Studio 5000) |
| **`Sts_Casc`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Vòng điều khiển đang ở chế độ Tầng hoặc Tỷ lệ (Cascade / Ratio Loop Mode) |
| **`Sts_Auto`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Vòng điều khiển đang ở chế độ Tự động (Automatic Loop Mode) |
| **`Sts_Man`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Vòng điều khiển đang ở chế độ Bằng tay (Manual Loop Mode) |
| **`Sts_NormLM`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Vòng điều khiển đang ở đúng chế độ Mặc định chuẩn (Normal Loop Mode theo Cfg_NormLM) |
| **`Sts_Initializing`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra CV của vòng điều khiển đang khởi tạo bám theo yêu cầu từ vòng thứ cấp phía sau (Secondary Loop) |
| **`Sts_InitReq`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang phát yêu cầu khởi tạo bám giá trị ngược lên vòng điều khiển sơ cấp phía trước (Primary Loop) |
| **`Sts_WindupHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Vòng điều khiển đang chạm bão hòa giới hạn trên (gửi lên chân Inp_WindupHi của vòng sơ cấp để chống bão hòa tích phân) |
| **`Sts_WindupLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Vòng điều khiển đang chạm bão hòa giới hạn dưới (gửi lên chân Inp_WindupLo của vòng sơ cấp để chống bão hòa tích phân) |
| **`Sts_ZCDBOn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Sai lệch PV đang nằm trong vùng chết qua điểm 0 (Zero-Crossing Deadband), ngõ ra CV được giữ nguyên không đổi |
| **`Sts_RatioClamped`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Hệ số tỷ lệ (Ratio) đang bị kẹp tại giới hạn Cfg_MinRatio hoặc Cfg_MaxRatio |
| **`Sts_SPBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đặt Setpoint (SP) không hợp lệ hoặc tín hiệu Cascade SP bị lỗi truyền thông |
| **`Sts_IntlkSP`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đặt SP đang bị cưỡng bức chuyển về giá trị an toàn (Cfg_IntlkSP) do cơ chế ngắt bảo vệ |
| **`Sts_SPHeld`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đặt SP đang được giữ ở giá trị tốt cuối cùng do cơ chế ngắt bảo vệ (Shed) |
| **`Sts_SPShedPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đặt SP được đặt bằng giá trị PV hiện tại do cơ chế ngắt bảo vệ |
| **`Sts_SPShed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đặt SP đang bị chi phối bởi cơ chế ngắt bảo vệ (Shed); 0 = SP bình thường |
| **`Sts_SPTrackPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đặt SP đang tự động bám theo giá trị PV thực tế (PV Tracking khi đang ở chế độ Manual) |
| **`Sts_SPClamped`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đặt Setpoint (SP) đang bị kẹp tại ngưỡng giới hạn Cfg_MinSP hoặc Cfg_MaxSP |
| **`Sts_SPRamping`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đặt SP đang trong quá trình tăng/giảm theo độ dốc tiến về Val_SPSet (0 = Đã hoàn tất Ramp) |
| **`Sts_SkipSPRoCLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang bỏ qua bộ giới hạn tốc độ thay đổi SP |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Xấu (Bad Quality) hoặc tín hiệu vượt ngoài dải lỗi (Out of Range) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Không chắc chắn (Uncertain Quality) |
| **`Sts_FFBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị bù nhiễu tiến cử (Feedforward Term) không hợp lệ |
| **`Sts_FFPrevBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị phản hồi Inp_FFPrev nằm ngoài dải -100..100% hoặc không phải là số hợp lệ (NaN) |
| **`Sts_CVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị CV bị lỗi chất lượng xấu, không hợp lệ hoặc lỗi truyền thông kênh xuất CV |
| **`Sts_CVPrevBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị phản hồi Inp_CVPrev nằm ngoài dải 0..100% hoặc không phải là số hợp lệ (NaN) |
| **`Sts_HandFdbkBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu phản hồi vị trí tay (Inp_HandFdbk / Tieback) bị lỗi chất lượng xấu hoặc mất truyền thông |
| **`Sts_IntlkCV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị ngõ ra CV đang bị cưỡng bức đưa về giá trị an toàn liên động (Cfg_IntlkCV) do tác động ngắt bảo vệ |
| **`Sts_CVHeld`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị ngõ ra CV đang được giữ nguyên tại giá trị tốt cuối cùng (Hold Last Good CV) do tác động ngắt bảo vệ (Shed) |
| **`Sts_CVShed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra CV đang bị chi phối bởi cơ chế ngắt bảo vệ (Shed); 0 = CV điều khiển bình thường từ Prog/Oper/Ovrd/PID |
| **`Sts_CVClamped`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị CV đang bị kẹp tại giới hạn Min/Max (dùng để hiển thị cờ kẹp trên HMI Faceplate) |
| **`Sts_CVRamping`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị CV đang thay đổi theo độ dốc tiến về Val_CVSet (0 = Đã đạt tới giá trị đặt) |
| **`Sts_Active`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị ngõ ra CV lớn hơn ngưỡng kích hoạt (Cfg_MaxInactiveCV), hiển thị biểu tượng hoạt động trên HMI |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Init`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Vòng điều khiển đang được khởi tạo ép về chế độ Bằng tay (Initialized to Manual Mode) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_RatioLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp hệ số tỷ lệ (Cfg_MinRatio / Cfg_MaxRatio) không hợp lệ |
| **`Err_SPLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp giá trị đặt Setpoint bị ngược hoặc không hợp lệ (Cfg_MinSP > Cfg_MaxSP) |
| **`Err_CVLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ ngõ ra CV không hợp lệ (Cfg_MinCV > Cfg_MaxCV) |
| **`Err_PVEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị kỹ thuật của PV không hợp lệ (Cfg_PVEUMin = Cfg_PVEUMax) |
| **`Err_CVEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn quy đổi đơn vị kỹ thuật của ngõ ra CV không hợp lệ (Cfg_CVEUMin = Cfg_CVEUMax) |
| **`Err_CVRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn đơn vị thô (Raw) của ngõ ra CV không hợp lệ (Cfg_CVRawMin = Cfg_CVRawMax) |
| **`Err_ZCDB`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Vùng chết qua điểm không của vòng điều khiển (Zero-Crossing Deadband) âm hoặc không hợp lệ |
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
| **`Sts_HiHiDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của độ lệch Rất Cao (High-High Deviation) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiHiDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của độ lệch Rất Cao (High-High Deviation) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiHiDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện độ lệch Rất Cao (High-High Deviation) |
| **`Alm_HiHiDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho độ lệch Rất Cao (High-High Deviation) |
| **`Ack_HiHiDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động độ lệch Rất Cao (High-High Deviation) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiHiDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Rất Cao (High-High Deviation) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiHiDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Rất Cao (High-High Deviation) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiHiDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Rất Cao (High-High Deviation) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của độ lệch Cao (High Deviation) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của độ lệch Cao (High Deviation) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện độ lệch Cao (High Deviation) |
| **`Alm_HiDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho độ lệch Cao (High Deviation) |
| **`Ack_HiDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động độ lệch Cao (High Deviation) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Cao (High Deviation) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Cao (High Deviation) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Cao (High Deviation) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của độ lệch Thấp (Low Deviation) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của độ lệch Thấp (Low Deviation) đã đếm xong, đang cho phép giám sát |
| **`Sts_LoDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện độ lệch Thấp (Low Deviation) |
| **`Alm_LoDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho độ lệch Thấp (Low Deviation) |
| **`Ack_LoDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động độ lệch Thấp (Low Deviation) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Thấp (Low Deviation) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Thấp (Low Deviation) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Thấp (Low Deviation) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoLoDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của độ lệch Rất Thấp (Low-Low Deviation) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoLoDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của độ lệch Rất Thấp (Low-Low Deviation) đã đếm xong, đang cho phép giám sát |
| **`Sts_LoLoDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện độ lệch Rất Thấp (Low-Low Deviation) |
| **`Alm_LoLoDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho độ lệch Rất Thấp (Low-Low Deviation) |
| **`Ack_LoLoDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động độ lệch Rất Thấp (Low-Low Deviation) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoLoDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Rất Thấp (Low-Low Deviation) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoLoDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Rất Thấp (Low-Low Deviation) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoLoDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Rất Thấp (Low-Low Deviation) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_PIDE`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_PIDE) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 59 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PIDE`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_CascSPNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tên thẻ (Tagname) của khối sơ cấp cấp tín hiệu Cascade SP khi nhấn nút điều hướng trên HMI |
| **`Cfg_CVEU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của ngõ ra điều khiển CV hiển thị trên màn hình HMI |
| **`Cfg_CVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tên thẻ (Tagname) của khối cơ cấu chấp hành CV đích khi nhấn nút điều hướng CV trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Advanced PID Control Loop'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'PID Loop'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_PVEU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến sơ cấp PV hiển thị trên màn hình HMI |
| **`Cfg_PVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tên thẻ (Tagname) của khối cảm biến PV đích khi nhấn nút điều hướng PV trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_PIDAdv'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`Fail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`HiDev`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho độ lệch Cao (High Deviation) |
| **`HiDevGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho độ lệch Cao (High Deviation) |
| **`HiHiDev`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho độ lệch Rất Cao (High-High Deviation) |
| **`HiHiDevGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho độ lệch Rất Cao (High-High Deviation) |
| **`HMI_HasAutotune`** | `BOOL` | `0` | 1 = Khối P_PIDE đã được cấu hình liên kết tính năng Tự động dò thông số (Autotune) trên khối PIDE |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_PIDE'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`LoDev`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho độ lệch Thấp (Low Deviation) |
| **`LoDevGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho độ lệch Thấp (Low Deviation) |
| **`LoLoDev`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho độ lệch Rất Thấp (Low-Low Deviation) |
| **`LoLoDevGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho độ lệch Rất Thấp (Low-Low Deviation) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Auto`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Chuyển vòng điều khiển sang chế độ Tự động (Automatic Loop Mode) |
| **`OCmd_Casc`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Chuyển vòng điều khiển sang chế độ Tầng / Tỷ lệ (Cascade / Ratio Loop Mode) |
| **`OCmd_Man`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Chuyển vòng điều khiển sang chế độ Bằng tay (Manual Loop Mode) |
| **`OCmd_NormLM`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Chuyển vòng điều khiển về chế độ Mặc định chuẩn (Normal Loop Mode theo Cfg_NormLM) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_SPRampStart`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Bắt đầu chạy quy trình thay đổi giá trị đặt SP theo độ dốc (Start Setpoint Ramping) |
| **`OCmd_SPRampStop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng quy trình thay đổi giá trị đặt SP theo độ dốc (Stop Setpoint Ramping) |
| **`ORdy_Auto`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Auto (Chuyển vòng điều khiển sang chế độ Tự động (Automatic Loop Mode)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Casc`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Casc (Chuyển vòng điều khiển sang chế độ Tầng / Tỷ lệ (Cascade / Ratio Loop Mode)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_CV`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_CV (Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_Man`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Man (Chuyển vòng điều khiển sang chế độ Bằng tay (Manual Loop Mode)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_NormLM`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_NormLM (Chuyển vòng điều khiển về chế độ Mặc định chuẩn (Normal Loop Mode theo Cfg_NormLM)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Ratio`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_Ratio (Hệ số tỷ lệ (Ratio, không thứ nguyên) khi vòng điều khiển chạy ở chế độ Cascade/Ratio), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SP`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_SP (Giá trị đặt chính (Setpoint - SP, tính theo đơn vị kỹ thuật PV EU) khi chạy chế độ Tự động (Auto)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_SPRampStart`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SPRampStart (Bắt đầu chạy quy trình thay đổi giá trị đặt SP theo độ dốc (Start Setpoint Ramping)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SPRampStop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SPRampStop (Dừng quy trình thay đổi giá trị đặt SP theo độ dốc (Stop Setpoint Ramping)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SPRampT`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_SPRampT (Thời gian tăng/giảm giá trị đặt SP đến đích (Setpoint Ramp Time, hợp lệ từ 0.0 đến 28800.0 giây)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_SPRoCLimDec`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_SPRoCLimDec (Giới hạn tốc độ giảm tối đa của giá trị đặt SP (Negative Rate-of-Change Limit, đơn vị PVEU/giây)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_SPRoCLimInc`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_SPRoCLimInc (Giới hạn tốc độ tăng tối đa của giá trị đặt SP (Positive Rate-of-Change Limit, đơn vị PVEU/giây)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_SPTarget`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_SPTarget (Giá trị đích cuối cùng của quá trình tăng/giảm SP theo độ dốc (Setpoint Ramp Target, đơn vị PVEU)), cho phép mở ô nhập số trên HMI Faceplate |
| **`OSet_CV`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU) |
| **`OSet_Ratio`** | `REAL` | `1` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Hệ số tỷ lệ (Ratio, không thứ nguyên) khi vòng điều khiển chạy ở chế độ Cascade/Ratio |
| **`OSet_SP`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị đặt chính (Setpoint - SP, tính theo đơn vị kỹ thuật PV EU) khi chạy chế độ Tự động (Auto) |
| **`OSet_SPRampT`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Thời gian tăng/giảm giá trị đặt SP đến đích (Setpoint Ramp Time, hợp lệ từ 0.0 đến 28800.0 giây) |
| **`OSet_SPRoCLimDec`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giới hạn tốc độ giảm tối đa của giá trị đặt SP (Negative Rate-of-Change Limit, đơn vị PVEU/giây) |
| **`OSet_SPRoCLimInc`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giới hạn tốc độ tăng tối đa của giá trị đặt SP (Positive Rate-of-Change Limit, đơn vị PVEU/giây) |
| **`OSet_SPTarget`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị đích cuối cùng của quá trình tăng/giảm SP theo độ dốc (Setpoint Ramp Target, đơn vị PVEU) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Fanout`
**Thuộc chương:** [Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)](../02_CHUONG_2_REGULATORY_PROCEDURAL_CONTROL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 84–88)  
**Tệp định nghĩa khối lệnh:** `P_Fanout_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Fanout`:** **63** Biến đầu vào (`Input Parameters`) | **61** Biến đầu ra (`Output Parameters`) | **26** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Fanout` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Phân nhánh tín hiệu tương tự (Analog Fanout / Split-Range)** từ 1 đầu vào sơ cấp (`Inp_CV`) ra tối đa **8 đầu ra thứ cấp** (`Out_CV1..8`). Mỗi nhánh có hệ số tỷ lệ (Gain/Slope $m_i$), độ dịch (Offset $b_i$) và kẹp giới hạn riêng, đồng thời cho phép tách lẻ từng nhánh sang điều khiển bằng tay trên HMI.
- **Khi nào CẦN sử dụng:** 1. **Điều khiển chia dải (Split-range):** 1 bộ PID nhiệt độ điều khiển cả van cấp nhiệt (mở khi CV từ `50..100%`) và van nước làm mát (mở khi CV từ `50..0%`). 2. **Điều khiển đồng tốc:** 1 bộ PID áp suất điều khiển đồng thời 4 biến tần quạt gió chạy song song.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi chỉ có 1 bộ PID điều khiển 1 cơ cấu chấp hành duy nhất.

### Quy trình 4 bước đấu nối và cấu hình `P_Fanout`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_CV`, `Inp_CV1InitVal`, `Inp_CV1InitReq`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasCV2`, `Cfg_HasCV3`, `Cfg_HasCV4`, `Cfg_HasCV5`, `Cfg_HasCV6`, `Cfg_HasCV7`, `Cfg_HasCV8`, `Cfg_HasMoreObj`, `Cfg_FixedInitVal`, `Cfg_UseFixedInit`, `Cfg_ShedHold`, `Cfg_HasCVNav`, `Cfg_HasNav`, `Cfg_CVEUMin`, `Cfg_CVEUMax`, `Cfg_CVMin`, `Cfg_CVMax`, `Cfg_CVRoCLim`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_CV_EU`, `Cfg_CV1_EU`, `Cfg_CV1_Label`, `Cfg_CV2_EU`, `Cfg_CV2_Label`, `Cfg_CV3_EU`, `Cfg_CV3_Label`, `Cfg_CV4_EU`, `Cfg_CV4_Label`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Fanout` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Fanout` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_Fanout` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_CV1`, `Out_CV2`, `Out_CV3`, `Out_CV4`, `Out_CV5`, `Out_CV6`, `Out_CV7`, `Out_CV8`, `Out_CVInitVal`, `Out_CVInitReq`, `Val_CVEUMin`, `Val_CVEUMax`, `Val_InpCV`, `Val_CV`, `Val_MinCVIn1`, `Val_MinCVIn2`, `Val_MinCVIn3`, `Val_MinCVIn4`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 63 Biến Đầu Vào (`Input Parameters`) của `P_Fanout`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_CV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào (Input CV, đơn vị kỹ thuật EU) nhận từ ngõ ra của khối điều khiển phía trước (Upstream block) |
| **`Inp_CV1InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị khởi tạo gửi ngược từ khối chấp hành thứ cấp số #1 (đơn vị kỹ thuật của nhánh CV1) |
| **`Inp_CV1InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Yêu cầu khởi tạo bám giá trị từ khối chấp hành thứ cấp số #1 (1 = Đang yêu cầu khởi tạo nhánh CV1) |
| **`Cfg_HasCV2`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV2 có được kết nối và sử dụng |
| **`Cfg_HasCV3`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV3 có được kết nối và sử dụng |
| **`Cfg_HasCV4`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV4 có được kết nối và sử dụng |
| **`Cfg_HasCV5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV5 có được kết nối và sử dụng |
| **`Cfg_HasCV6`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV6 có được kết nối và sử dụng |
| **`Cfg_HasCV7`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV7 có được kết nối và sử dụng |
| **`Cfg_HasCV8`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV8 có được kết nối và sử dụng |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_FixedInitVal`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị khởi tạo cố định (đơn vị Inp_CV EU) gửi về khối sơ cấp khi Cfg_UseFixedInit = 1 |
| **`Cfg_UseFixedInit`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng hằng số Cfg_FixedInitVal để khởi tạo khối sơ cấp; 0 = Sử dụng giá trị phản hồi từ nhánh CV1 (Inp_CV1InitVal) |
| **`Cfg_ShedHold`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra CV khi vi phạm Khóa liên động (Interlock Trip): 1 = Giữ nguyên giá trị CV hiện tại (Hold Last State); 0 = Đưa ngõ ra CV về giá trị an toàn định trước (Cfg_IntlkCV) |
| **`Cfg_HasCVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép hiển thị nút điều hướng nhảy tới Faceplate của khối cơ cấu chấp hành CV trên HMI |
| **`Cfg_HasNav`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit (16-bit) bật nút điều hướng nhảy tới Faceplate nguyên nhân cho từng điều kiện trong 16 kênh (Bit = 1 là bật) |
| **`Cfg_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật đầu ra CV tương ứng với Cfg_CVRawMin (thường là 0.0%) |
| **`Cfg_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật đầu ra CV tương ứng với Cfg_CVRawMax (thường là 100.0%) |
| **`Cfg_CVMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu của tín hiệu CV (đơn vị kỹ thuật EU) |
| **`Cfg_CVMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa của tín hiệu CV (đơn vị kỹ thuật EU) |
| **`Cfg_CVRoCLim`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn tốc độ biến thiên tối đa của ngõ ra điều khiển CV (CV Rate-of-Change Limit, đơn vị CVEU/giây) |
| **`Cfg_CV1Ratio`** | `REAL` | `-2` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV1 (Out_CV1 = Inp_CV × Ratio1 + Offset1) |
| **`Cfg_CV2Ratio`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV2 (Out_CV2 = Inp_CV × Ratio2 + Offset2) |
| **`Cfg_CV3Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV3 (Out_CV3 = Inp_CV × Ratio3 + Offset3) |
| **`Cfg_CV4Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV4 (Out_CV4 = Inp_CV × Ratio4 + Offset4) |
| **`Cfg_CV5Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV5 (Out_CV5 = Inp_CV × Ratio5 + Offset5) |
| **`Cfg_CV6Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV6 (Out_CV6 = Inp_CV × Ratio6 + Offset6) |
| **`Cfg_CV7Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV7 (Out_CV7 = Inp_CV × Ratio7 + Offset7) |
| **`Cfg_CV8Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV8 (Out_CV8 = Inp_CV × Ratio8 + Offset8) |
| **`Cfg_CV1Offset`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV1 (đơn vị kỹ thuật EU) |
| **`Cfg_CV2Offset`** | `REAL` | `-100` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV2 (đơn vị kỹ thuật EU) |
| **`Cfg_CV3Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV3 (đơn vị kỹ thuật EU) |
| **`Cfg_CV4Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV4 (đơn vị kỹ thuật EU) |
| **`Cfg_CV5Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV5 (đơn vị kỹ thuật EU) |
| **`Cfg_CV6Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV6 (đơn vị kỹ thuật EU) |
| **`Cfg_CV7Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV7 (đơn vị kỹ thuật EU) |
| **`Cfg_CV8Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV8 (đơn vị kỹ thuật EU) |
| **`Cfg_CV1Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV1 (đơn vị kỹ thuật EU) |
| **`Cfg_CV2Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV2 (đơn vị kỹ thuật EU) |
| **`Cfg_CV3Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV3 (đơn vị kỹ thuật EU) |
| **`Cfg_CV4Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV4 (đơn vị kỹ thuật EU) |
| **`Cfg_CV5Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV5 (đơn vị kỹ thuật EU) |
| **`Cfg_CV6Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV6 (đơn vị kỹ thuật EU) |
| **`Cfg_CV7Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV7 (đơn vị kỹ thuật EU) |
| **`Cfg_CV8Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV8 (đơn vị kỹ thuật EU) |
| **`Cfg_CV1Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV1 (đơn vị kỹ thuật EU) |
| **`Cfg_CV2Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV2 (đơn vị kỹ thuật EU) |
| **`Cfg_CV3Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV3 (đơn vị kỹ thuật EU) |
| **`Cfg_CV4Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV4 (đơn vị kỹ thuật EU) |
| **`Cfg_CV5Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV5 (đơn vị kỹ thuật EU) |
| **`Cfg_CV6Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV6 (đơn vị kỹ thuật EU) |
| **`Cfg_CV7Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV7 (đơn vị kỹ thuật EU) |
| **`Cfg_CV8Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV8 (đơn vị kỹ thuật EU) |
| **`Cfg_CV1TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV1 sau khi kết thúc khởi tạo |
| **`Cfg_CV2TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV2 sau khi kết thúc khởi tạo |
| **`Cfg_CV3TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV3 sau khi kết thúc khởi tạo |
| **`Cfg_CV4TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV4 sau khi kết thúc khởi tạo |
| **`Cfg_CV5TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV5 sau khi kết thúc khởi tạo |
| **`Cfg_CV6TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV6 sau khi kết thúc khởi tạo |
| **`Cfg_CV7TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV7 sau khi kết thúc khởi tạo |
| **`Cfg_CV8TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV8 sau khi kết thúc khởi tạo |
| **`Cfg_CVDecPlcs`** | `SINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị ngõ ra CV trên màn hình HMI (từ 0 đến 6 chữ số) |

## 3. Bảng chi tiết 61 Biến Đầu Ra (`Output Parameters`) của `P_Fanout`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_CV1`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV1 (đơn vị kỹ thuật EU, tính theo công thức Out_CV1 = Inp_CV × Ratio1 + Offset1) |
| **`Out_CV2`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV2 (đơn vị kỹ thuật EU, tính theo công thức Out_CV2 = Inp_CV × Ratio2 + Offset2) |
| **`Out_CV3`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV3 (đơn vị kỹ thuật EU, tính theo công thức Out_CV3 = Inp_CV × Ratio3 + Offset3) |
| **`Out_CV4`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV4 (đơn vị kỹ thuật EU, tính theo công thức Out_CV4 = Inp_CV × Ratio4 + Offset4) |
| **`Out_CV5`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV5 (đơn vị kỹ thuật EU, tính theo công thức Out_CV5 = Inp_CV × Ratio5 + Offset5) |
| **`Out_CV6`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV6 (đơn vị kỹ thuật EU, tính theo công thức Out_CV6 = Inp_CV × Ratio6 + Offset6) |
| **`Out_CV7`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV7 (đơn vị kỹ thuật EU, tính theo công thức Out_CV7 = Inp_CV × Ratio7 + Offset7) |
| **`Out_CV8`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV8 (đơn vị kỹ thuật EU, tính theo công thức Out_CV8 = Inp_CV × Ratio8 + Offset8) |
| **`Out_CVInitVal`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị khởi tạo (Initialization Value, đơn vị EU) gửi ngược lên khối điều khiển phía trước để chuyển mạch không giật |
| **`Out_CVInitReq`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Yêu cầu khởi tạo bám giá trị (Initialization Request: 1 = Yêu cầu khởi tạo) gửi ngược lên khối điều khiển phía trước (Upstream block) |
| **`Val_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đơn vị kỹ thuật ngõ ra CV = MIN(Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đơn vị kỹ thuật ngõ ra CV = MAX(Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_InpCV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tín hiệu đầu vào Inp_CV trước khi kẹp giới hạn hoặc giới hạn tốc độ (đơn vị EU) |
| **`Val_CV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến điều khiển CV hiện hành (sau khi đã kẹp giới hạn và giới hạn tốc độ biến thiên, đơn vị EU) |
| **`Val_MinCVIn1`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV1 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn2`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV2 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn3`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV3 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn4`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV4 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn5`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV5 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn6`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV6 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn7`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV7 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn8`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV8 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn1`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV1 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn2`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV2 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn3`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV3 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn4`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV4 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn5`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV5 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn6`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV6 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn7`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV7 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn8`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV8 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Sts_CVInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào Inp_CV là số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CVLimited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Val_CV đã chạm và bị giới hạn tại ngưỡng Cfg_MinCV hoặc Cfg_MaxCV |
| **`Sts_CV1InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV1InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV1Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV1 đang bị kẹp giới hạn tại ngưỡng Cfg_CV1Min hoặc Cfg_CV1Max |
| **`Sts_CV2InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV2InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV2Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV2 đang bị kẹp giới hạn tại ngưỡng Cfg_CV2Min hoặc Cfg_CV2Max |
| **`Sts_CV3InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV3InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV3Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV3 đang bị kẹp giới hạn tại ngưỡng Cfg_CV3Min hoặc Cfg_CV3Max |
| **`Sts_CV4InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV4InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV4Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV4 đang bị kẹp giới hạn tại ngưỡng Cfg_CV4Min hoặc Cfg_CV4Max |
| **`Sts_CV5InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV5InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV5Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV5 đang bị kẹp giới hạn tại ngưỡng Cfg_CV5Min hoặc Cfg_CV5Max |
| **`Sts_CV6InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV6InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV6Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV6 đang bị kẹp giới hạn tại ngưỡng Cfg_CV6Min hoặc Cfg_CV6Max |
| **`Sts_CV7InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV7InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV7Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV7 đang bị kẹp giới hạn tại ngưỡng Cfg_CV7Min hoặc Cfg_CV7Max |
| **`Sts_CV8InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV8InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV8Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV8 đang bị kẹp giới hạn tại ngưỡng Cfg_CV8Min hoặc Cfg_CV8Max |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Ngưỡng kẹp giới hạn trên của PV nhỏ hơn ngưỡng kẹp giới hạn dưới (Cfg_PVHiLim < Cfg_PVLoLim) |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_CV1Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV1 bị ngược (Cfg_CV1Max < Cfg_CV1Min) |
| **`Err_CV2Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV2 bị ngược (Cfg_CV2Max < Cfg_CV2Min) |
| **`Err_CV3Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV3 bị ngược (Cfg_CV3Max < Cfg_CV3Min) |
| **`Err_CV4Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV4 bị ngược (Cfg_CV4Max < Cfg_CV4Min) |
| **`Err_CV5Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV5 bị ngược (Cfg_CV5Max < Cfg_CV5Min) |
| **`Err_CV6Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV6 bị ngược (Cfg_CV6Max < Cfg_CV6Min) |
| **`Err_CV7Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV7 bị ngược (Cfg_CV7Max < Cfg_CV7Min) |
| **`Err_CV8Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV8 bị ngược (Cfg_CV8Max < Cfg_CV8Min) |
| **`P_Fanout`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Fanout) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 26 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Fanout`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_CV_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của ngõ ra điều khiển CV hiển thị trên màn hình HMI |
| **`Cfg_CV1_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #1 (CV1) hiển thị trên HMI |
| **`Cfg_CV1_Label`** | `STRING_20` | `'Output 1'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #1 (CV1) hiển thị trên HMI |
| **`Cfg_CV2_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #2 (CV2) hiển thị trên HMI |
| **`Cfg_CV2_Label`** | `STRING_20` | `'Output 2'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #2 (CV2) hiển thị trên HMI |
| **`Cfg_CV3_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #3 (CV3) hiển thị trên HMI |
| **`Cfg_CV3_Label`** | `STRING_20` | `'Output 3'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #3 (CV3) hiển thị trên HMI |
| **`Cfg_CV4_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #4 (CV4) hiển thị trên HMI |
| **`Cfg_CV4_Label`** | `STRING_20` | `'Output 4'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #4 (CV4) hiển thị trên HMI |
| **`Cfg_CV5_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #5 (CV5) hiển thị trên HMI |
| **`Cfg_CV5_Label`** | `STRING_20` | `'Output 5'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #5 (CV5) hiển thị trên HMI |
| **`Cfg_CV6_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #6 (CV6) hiển thị trên HMI |
| **`Cfg_CV6_Label`** | `STRING_20` | `'Output 6'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #6 (CV6) hiển thị trên HMI |
| **`Cfg_CV7_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #7 (CV7) hiển thị trên HMI |
| **`Cfg_CV7_Label`** | `STRING_20` | `'Output 7'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #7 (CV7) hiển thị trên HMI |
| **`Cfg_CV8_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #8 (CV8) hiển thị trên HMI |
| **`Cfg_CV8_Label`** | `STRING_20` | `'Output 8'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #8 (CV8) hiển thị trên HMI |
| **`Cfg_CVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tên thẻ (Tagname) của khối cơ cấu chấp hành CV đích khi nhấn nút điều hướng CV trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Fanout'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Analog Fanout'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_NavTag`** | `STRING_NavTag[8]` | `{...}` | [Biến cấu hình thông số] Mảng 16 tên thẻ (Tagname) đích tương ứng với 16 nút điều hướng điều kiện liên động/cho phép trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_Fanout'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Fanout'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_HiLoSel`
**Thuộc chương:** [Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)](../02_CHUONG_2_REGULATORY_PROCEDURAL_CONTROL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 89–91)  
**Tệp định nghĩa khối lệnh:** `P_HiLoSel_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_HiLoSel`:** **46** Biến đầu vào (`Input Parameters`) | **21** Biến đầu ra (`Output Parameters`) | **17** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_HiLoSel` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Chọn giá trị Cao nhất hoặc Thấp nhất (High/Low Selector)** từ tối đa **6 tín hiệu đầu vào CV** (`Inp_CV1..6`) để xuất ra 1 cơ cấu chấp hành chung (`Out_CV`), đồng thời xuất tín hiệu bám (`Out_CVxTrack`) trả ngược về các bộ PID không được chọn để chống bão hòa tích phân (Anti-Reset Windup).
- **Khi nào CẦN sử dụng:** **Bài toán điều khiển giành quyền bảo vệ (Override / Constraint Control):** Ví dụ 1 bơm biến tần bình thường chạy theo PID lưu lượng, nhưng có thêm PID bảo vệ áp suất đầu đẩy không quá cao và PID bảo vệ áp suất đầu hút không quá thấp. `P_HiLoSel` (chế độ Low Select) sẽ tự động chọn đầu ra nhỏ nhất để bảo vệ đường ống và bơm.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi muốn chọn Min/Max của các **cảm biến đầu vào PV** (trước khi vào PID) → Dùng [`P_AInDual.md`](./P_AInDual.md) hoặc [`P_AInMulti.md`](./P_AInMulti.md).

### Quy trình 4 bước đấu nối và cấu hình `P_HiLoSel`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_1`, `Inp_E1`, `Inp_Kp1`, `Inp_2`, `Inp_E2`, `Inp_Kp2`, `Inp_3`, `Inp_E3`, `Inp_Kp3`, `Inp_4`, `Inp_E4`, `Inp_Kp4`, `Inp_5`, `Inp_E5`, `Inp_Kp5`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HiLoSel`, `Cfg_HasInp1`, `Cfg_UseInp1`, `Cfg_Inp1Offset`, `Cfg_HasInp2`, `Cfg_UseInp2`, `Cfg_Inp2Offset`, `Cfg_HasInp3`, `Cfg_UseInp3`, `Cfg_Inp3Offset`, `Cfg_HasInp4`, `Cfg_UseInp4`, `Cfg_Inp4Offset`, `Cfg_HasInp5`, `Cfg_UseInp5`, `Cfg_Inp5Offset`, `Cfg_HasInp6`, `Cfg_UseInp6`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Inp1_Label`, `Cfg_Inp2_Label`, `Cfg_Inp3_Label`, `Cfg_Inp4_Label`, `Cfg_Inp5_Label`, `Cfg_Inp6_Label`, `Cfg_Label`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_HiLoSel` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_HiLoSel` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_HiLoSel` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Inp1Prev`, `Out_UseInp1Prev`, `Out_Inp2Prev`, `Out_UseInp2Prev`, `Out_Inp3Prev`, `Out_UseInp3Prev`, `Out_Inp4Prev`, `Out_UseInp4Prev`, `Out_Inp5Prev`, `Out_UseInp5Prev`, `Out_Inp6Prev`, `Out_UseInp6Prev`, `Val_Out`, `Val_Selected`, `Sts_MaintByp`, `Sts_Err`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 46 Biến Đầu Vào (`Input Parameters`) của `P_HiLoSel`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào số #1 đưa vào bộ chọn Cao/Thấp (P_HiLoSel) |
| **`Inp_E1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Sai lệch điều khiển (Loop Error) từ bộ PID sơ cấp số #1 (tùy chọn để tính độ lệch Offset chống vọt lố) |
| **`Inp_Kp1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Hệ số khuếch đại tỷ lệ (Proportional Gain Kp) từ bộ PID sơ cấp số #1 (tùy chọn để tính độ lệch Offset) |
| **`Inp_2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào số #2 đưa vào bộ chọn Cao/Thấp (P_HiLoSel) |
| **`Inp_E2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Sai lệch điều khiển (Loop Error) từ bộ PID sơ cấp số #2 (tùy chọn để tính độ lệch Offset chống vọt lố) |
| **`Inp_Kp2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Hệ số khuếch đại tỷ lệ (Proportional Gain Kp) từ bộ PID sơ cấp số #2 (tùy chọn để tính độ lệch Offset) |
| **`Inp_3`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào số #3 đưa vào bộ chọn Cao/Thấp (P_HiLoSel) |
| **`Inp_E3`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Sai lệch điều khiển (Loop Error) từ bộ PID sơ cấp số #3 (tùy chọn để tính độ lệch Offset chống vọt lố) |
| **`Inp_Kp3`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Hệ số khuếch đại tỷ lệ (Proportional Gain Kp) từ bộ PID sơ cấp số #3 (tùy chọn để tính độ lệch Offset) |
| **`Inp_4`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào số #4 đưa vào bộ chọn Cao/Thấp (P_HiLoSel) |
| **`Inp_E4`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Sai lệch điều khiển (Loop Error) từ bộ PID sơ cấp số #4 (tùy chọn để tính độ lệch Offset chống vọt lố) |
| **`Inp_Kp4`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Hệ số khuếch đại tỷ lệ (Proportional Gain Kp) từ bộ PID sơ cấp số #4 (tùy chọn để tính độ lệch Offset) |
| **`Inp_5`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào số #5 đưa vào bộ chọn Cao/Thấp (P_HiLoSel) |
| **`Inp_E5`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Sai lệch điều khiển (Loop Error) từ bộ PID sơ cấp số #5 (tùy chọn để tính độ lệch Offset chống vọt lố) |
| **`Inp_Kp5`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Hệ số khuếch đại tỷ lệ (Proportional Gain Kp) từ bộ PID sơ cấp số #5 (tùy chọn để tính độ lệch Offset) |
| **`Inp_6`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào số #6 đưa vào bộ chọn Cao/Thấp (P_HiLoSel) |
| **`Inp_E6`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Sai lệch điều khiển (Loop Error) từ bộ PID sơ cấp số #6 (tùy chọn để tính độ lệch Offset chống vọt lố) |
| **`Inp_Kp6`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Hệ số khuếch đại tỷ lệ (Proportional Gain Kp) từ bộ PID sơ cấp số #6 (tùy chọn để tính độ lệch Offset) |
| **`Inp_InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị khởi tạo gửi ngược từ khối phía sau (Downstream Initialization Value) để đồng bộ không giật |
| **`Inp_InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Yêu cầu khởi tạo bám giá trị từ khối phía sau gửi ngược về (1 = Khởi tạo ngõ ra bằng giá trị Inp_InitVal) |
| **`Cfg_HiLoSel`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Chế độ chọn tín hiệu của khối P_HiLoSel: 1 = Chọn giá trị Lớn nhất (High-Select); 0 = Chọn giá trị Nhỏ nhất (Low-Select) |
| **`Cfg_HasInp1`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kênh đầu vào số #1 (Inp_1) có được kết nối sử dụng |
| **`Cfg_UseInp1`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh đầu vào số #1 (Inp_1) vào bộ chọn Min/Max; 0 = Tạm loại kênh #1 khỏi bộ chọn (Maintenance) |
| **`Cfg_Inp1Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cộng thêm độ lệch bù Kp1 × E1 vào giá trị khởi tạo trả về bộ PID sơ cấp #1 để chống vọt lố khi chuyển mạch; 0 = Không cộng Offset |
| **`Cfg_HasInp2`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kênh đầu vào số #2 (Inp_2) có được kết nối sử dụng |
| **`Cfg_UseInp2`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh đầu vào số #2 (Inp_2) vào bộ chọn Min/Max; 0 = Tạm loại kênh #2 khỏi bộ chọn (Maintenance) |
| **`Cfg_Inp2Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cộng thêm độ lệch bù Kp2 × E2 vào giá trị khởi tạo trả về bộ PID sơ cấp #2 để chống vọt lố khi chuyển mạch; 0 = Không cộng Offset |
| **`Cfg_HasInp3`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kênh đầu vào số #3 (Inp_3) có được kết nối sử dụng |
| **`Cfg_UseInp3`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh đầu vào số #3 (Inp_3) vào bộ chọn Min/Max; 0 = Tạm loại kênh #3 khỏi bộ chọn (Maintenance) |
| **`Cfg_Inp3Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cộng thêm độ lệch bù Kp3 × E3 vào giá trị khởi tạo trả về bộ PID sơ cấp #3 để chống vọt lố khi chuyển mạch; 0 = Không cộng Offset |
| **`Cfg_HasInp4`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kênh đầu vào số #4 (Inp_4) có được kết nối sử dụng |
| **`Cfg_UseInp4`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh đầu vào số #4 (Inp_4) vào bộ chọn Min/Max; 0 = Tạm loại kênh #4 khỏi bộ chọn (Maintenance) |
| **`Cfg_Inp4Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cộng thêm độ lệch bù Kp4 × E4 vào giá trị khởi tạo trả về bộ PID sơ cấp #4 để chống vọt lố khi chuyển mạch; 0 = Không cộng Offset |
| **`Cfg_HasInp5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kênh đầu vào số #5 (Inp_5) có được kết nối sử dụng |
| **`Cfg_UseInp5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh đầu vào số #5 (Inp_5) vào bộ chọn Min/Max; 0 = Tạm loại kênh #5 khỏi bộ chọn (Maintenance) |
| **`Cfg_Inp5Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cộng thêm độ lệch bù Kp5 × E5 vào giá trị khởi tạo trả về bộ PID sơ cấp #5 để chống vọt lố khi chuyển mạch; 0 = Không cộng Offset |
| **`Cfg_HasInp6`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kênh đầu vào số #6 (Inp_6) có được kết nối sử dụng |
| **`Cfg_UseInp6`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh đầu vào số #6 (Inp_6) vào bộ chọn Min/Max; 0 = Tạm loại kênh #6 khỏi bộ chọn (Maintenance) |
| **`Cfg_Inp6Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cộng thêm độ lệch bù Kp6 × E6 vào giá trị khởi tạo trả về bộ PID sơ cấp #6 để chống vọt lố khi chuyển mạch; 0 = Không cộng Offset |
| **`Cfg_DecPlcs`** | `SINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho tốc độ/giá trị trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasOutNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép hiển thị nút điều hướng nhảy tới Faceplate của khối thiết bị ngõ ra trên HMI |
| **`Cfg_HasNav`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit (16-bit) bật nút điều hướng nhảy tới Faceplate nguyên nhân cho từng điều kiện trong 16 kênh (Bit = 1 là bật) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_OutMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho ngõ ra của bộ chọn P_HiLoSel |
| **`Cfg_OutMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho ngõ ra của bộ chọn P_HiLoSel |

## 3. Bảng chi tiết 21 Biến Đầu Ra (`Output Parameters`) của `P_HiLoSel`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Out_Inp1Prev`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị phản hồi (Tieback/Feedback) trả ngược về bộ điều khiển sơ cấp số #1 để chống bão hòa tích phân |
| **`Out_UseInp1Prev`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ yêu cầu bộ điều khiển sơ cấp số #1 bám theo giá trị phản hồi Out_Inp1Prev (khi kênh #1 không được chọn) |
| **`Out_Inp2Prev`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị phản hồi (Tieback/Feedback) trả ngược về bộ điều khiển sơ cấp số #2 để chống bão hòa tích phân |
| **`Out_UseInp2Prev`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ yêu cầu bộ điều khiển sơ cấp số #2 bám theo giá trị phản hồi Out_Inp2Prev (khi kênh #2 không được chọn) |
| **`Out_Inp3Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị phản hồi (Tieback/Feedback) trả ngược về bộ điều khiển sơ cấp số #3 để chống bão hòa tích phân |
| **`Out_UseInp3Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ yêu cầu bộ điều khiển sơ cấp số #3 bám theo giá trị phản hồi Out_Inp3Prev (khi kênh #3 không được chọn) |
| **`Out_Inp4Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị phản hồi (Tieback/Feedback) trả ngược về bộ điều khiển sơ cấp số #4 để chống bão hòa tích phân |
| **`Out_UseInp4Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ yêu cầu bộ điều khiển sơ cấp số #4 bám theo giá trị phản hồi Out_Inp4Prev (khi kênh #4 không được chọn) |
| **`Out_Inp5Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị phản hồi (Tieback/Feedback) trả ngược về bộ điều khiển sơ cấp số #5 để chống bão hòa tích phân |
| **`Out_UseInp5Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ yêu cầu bộ điều khiển sơ cấp số #5 bám theo giá trị phản hồi Out_Inp5Prev (khi kênh #5 không được chọn) |
| **`Out_Inp6Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị phản hồi (Tieback/Feedback) trả ngược về bộ điều khiển sơ cấp số #6 để chống bão hòa tích phân |
| **`Out_UseInp6Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ yêu cầu bộ điều khiển sơ cấp số #6 bám theo giá trị phản hồi Out_Inp6Prev (khi kênh #6 không được chọn) |
| **`Val_Out`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu ra đã được chọn (Min hoặc Max của các đầu vào) để hiển thị trên HMI |
| **`Val_Selected`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã số kênh đầu vào đang được bộ P_HiLoSel chọn xuất ra: 0 = Ngưỡng kẹp dưới (Min), 1..6 = Kênh Inp_1..Inp_6, 7 = Ngưỡng kẹp trên (Max), 8 = Giá trị khởi tạo Inp_InitVal |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Has`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Bắt buộc phải bật ít nhất một kênh cảm biến đầu vào (Cfg_HasPVx = 1) |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Ngưỡng kẹp giới hạn trên của PV nhỏ hơn ngưỡng kẹp giới hạn dưới (Cfg_PVHiLim < Cfg_PVLoLim) |
| **`P_HiLoSel`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_HiLoSel) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 17 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_HiLoSel`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'High or Low Selector'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật (Engineering Units, ví dụ: "°C", "bar", "m3/h", "%") hiển thị trên màn hình HMI |
| **`Cfg_Inp1_Label`** | `STRING_20` | `'Input 1'` | [Biến cấu hình thông số] Nhãn mô tả cho kênh đầu vào số #1 hiển thị trên HMI |
| **`Cfg_Inp2_Label`** | `STRING_20` | `'Input 2'` | [Biến cấu hình thông số] Nhãn mô tả cho kênh đầu vào số #2 hiển thị trên HMI |
| **`Cfg_Inp3_Label`** | `STRING_20` | `'Input 3'` | [Biến cấu hình thông số] Nhãn mô tả cho kênh đầu vào số #3 hiển thị trên HMI |
| **`Cfg_Inp4_Label`** | `STRING_20` | `'Input 4'` | [Biến cấu hình thông số] Nhãn mô tả cho kênh đầu vào số #4 hiển thị trên HMI |
| **`Cfg_Inp5_Label`** | `STRING_20` | `'Input 5'` | [Biến cấu hình thông số] Nhãn mô tả cho kênh đầu vào số #5 hiển thị trên HMI |
| **`Cfg_Inp6_Label`** | `STRING_20` | `'Input 6'` | [Biến cấu hình thông số] Nhãn mô tả cho kênh đầu vào số #6 hiển thị trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'High/Low Selector'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_NavTag`** | `STRING_NavTag[6]` | `{...}` | [Biến cấu hình thông số] Mảng 16 tên thẻ (Tagname) đích tương ứng với 16 nút điều hướng điều kiện liên động/cho phép trên HMI |
| **`Cfg_OutNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tên thẻ (Tagname) của khối thiết bị đầu ra đích khi nhấn nút điều hướng trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_HiLoSel'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_HasMore`** | `BOOL` | `0` | Cờ nội bộ báo cho HMI hiển thị nút mở rộng thông tin |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_HiLoSel'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_DBC`
**Thuộc chương:** [Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)](../02_CHUONG_2_REGULATORY_PROCEDURAL_CONTROL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 92–95)  
**Tệp định nghĩa khối lệnh:** `P_DBC_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_DBC`:** **102** Biến đầu vào (`Input Parameters`) | **82** Biến đầu ra (`Output Parameters`) | **37** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_DBC` trong Studio 5000
- **Chức năng cốt lõi:** Bộ điều khiển **Đóng/Ngắt có Vùng chết (Deadband / Bang-Bang / On-Off Controller)**. So sánh PV với ngưỡng `Raise` (Tăng) và ngưỡng `Lower` (Giảm) để đóng/ngắt 2 ngõ ra `Out_Raise` / `Out_Lower` hoặc cặp ngõ ra chốt Flip-Flop `Out_Q` / `Out_QNot`.
- **Khi nào CẦN sử dụng:** 1. **Bơm cấp/xả bồn tự động:** Mức nước xuống dưới `20%` thì bật bơm cấp (`Out_Q = 1`), giữ bơm chạy liên tục đến khi mức đạt `85%` mới ngắt. 2. **Bộ điều nhiệt (Thermostat):** Bật thanh sấy khi nhiệt độ thấp hơn ngưỡng dưới, bật quạt làm mát khi vượt ngưỡng trên.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi cơ cấu chấp hành thay đổi tuyến tính liên tục 0–100% cần độ chính xác cao → Dùng [`P_PIDE.md`](./P_PIDE.md).

### Quy trình 4 bước đấu nối và cấu hình `P_DBC`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_PV`, `Inp_PVSrcQ`, `Inp_PVNotify`, `Inp_PVBad`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_OvrdRaiseSP`, `Inp_OvrdLowerSP`, `Inp_ExtInh`, `Inp_HiDevGate`, `Inp_LoDevGate`, `Inp_HiRoCIncGate`, `Inp_HiRoCDecGate`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_PVDecPlcs`, `Cfg_SetTrack`, `Cfg_HasMoreObj`, `Cfg_HasPVNav`, `Cfg_HasOutNav`, `Cfg_HasHiDevAlm`, `Cfg_HasLoDevAlm`, `Cfg_HasHiRoCIncAlm`, `Cfg_HasHiRoCDecAlm`, `Cfg_HiDevResetReqd`, `Cfg_LoDevResetReqd`, `Cfg_HiRoCIncResetReqd`, `Cfg_HiRoCDecResetReqd`, `Cfg_HiDevAckReqd`, `Cfg_LoDevAckReqd`, `Cfg_HiRoCIncAckReqd`, `Cfg_HiRoCDecAckReqd`, `Cfg_HiDevSeverity`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_OutNavTag`, `Cfg_PVEU`, `Cfg_PVNavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_DBC` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_DBC` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_LowerSP`, `PSet_RaiseSP`, `PSet_Owner`, `PCmd_Raise`, `PCmd_Lower`, `PCmd_None`, `PCmd_Auto`, `PCmd_Man`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_HiDevAck`, `PCmd_LoDevAck`, `PCmd_HiRoCIncAck`, `PCmd_HiRoCDecAck`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Raise`, `Out_Lower`, `Out_Q`, `Out_Qnot`, `Val_PV`, `Val_RoC`, `Val_LowerSP`, `Val_RaiseSP`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Raise`, `Sts_Lower`, `Sts_Auto`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 102 Biến Đầu Vào (`Input Parameters`) của `P_DBC`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_PV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào (Process Variable - PV) đọc từ cảm biến hoặc thẻ Analog/Discrete Input |
| **`Inp_PVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu PV đầu vào (kiểu liệt kê enum, nối từ chân SrcQ của khối kênh P_AIChan nếu có) |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và trạng thái xác nhận (enum) của đối tượng PV liên kết |
| **`Inp_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc lỗi truyền thông đầu vào PV (1 = Lỗi/Bad, 0 = Tốt/OK — thường nối từ bit Channel Fault của thẻ I/O) |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh khi ở chế độ Cưỡng bức (Override): 0 = Không tác động, 1 = Bật ngõ ra Raise (ở Manual), 2 = Bật ngõ ra Lower (ở Manual), 3 = Tắt cả hai ngõ ra, 4 = Chuyển sang chế độ Manual, 5 = Chuyển sang chế độ Auto |
| **`Inp_OvrdRaiseSP`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Ngưỡng đặt Trên (Raise SP) khi ở chế độ Cưỡng bức (đơn vị EU) |
| **`Inp_OvrdLowerSP`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Ngưỡng đặt Dưới (Lower SP) khi ở chế độ Cưỡng bức (đơn vị EU) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HiDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát độ lệch Cao (High Deviation): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát độ lệch Thấp (Low Deviation): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_HiRoCIncGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát tốc độ tăng Cao (High Increasing Rate-of-Change): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_HiRoCDecGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát tốc độ giảm Cao (High Decreasing Rate-of-Change): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_SetTrack`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasPVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép hiển thị nút điều hướng nhảy tới Faceplate của khối cảm biến PV trên HMI |
| **`Cfg_HasOutNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép hiển thị nút điều hướng nhảy tới Faceplate của khối thiết bị ngõ ra trên HMI |
| **`Cfg_HasHiDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo độ lệch Cao (High Deviation): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo độ lệch Thấp (Low Deviation): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasHiRoCIncAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo tốc độ tăng Cao (High Increasing Rate-of-Change): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasHiRoCDecAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo tốc độ giảm Cao (High Decreasing Rate-of-Change): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HiDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo độ lệch Cao (High Deviation): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo độ lệch Thấp (Low Deviation): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiRoCIncResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo tốc độ tăng Cao (High Increasing Rate-of-Change): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiRoCDecResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo tốc độ giảm Cao (High Decreasing Rate-of-Change): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo độ lệch Cao (High Deviation): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo độ lệch Thấp (Low Deviation): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiRoCIncAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo tốc độ tăng Cao (High Increasing Rate-of-Change): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiRoCDecAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo tốc độ giảm Cao (High Decreasing Rate-of-Change): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiDevSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo độ lệch Cao (High Deviation) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Min (PV EU Min — hỗ trợ cả quy đổi ngược nếu đặt EUMin > EUMax) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Max (PV EU Max — hỗ trợ cả quy đổi ngược nếu đặt EUMax < EUMin) |
| **`Cfg_SPHiLim`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho các ngưỡng đặt Raise/Lower Setpoint (đơn vị EU) |
| **`Cfg_SPLoLim`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho các ngưỡng đặt Raise/Lower Setpoint (đơn vị EU) |
| **`Cfg_RaiseDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết hồi tiếp (Deadband, đơn vị EU) cho ngõ ra Tăng (Out_Raise) |
| **`Cfg_LowerDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết hồi tiếp (Deadband, đơn vị EU) cho ngõ ra Giảm (Out_Lower) |
| **`Cfg_RateTime`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Đơn vị cơ sở thời gian để tính tốc độ biến thiên (Rate of Change): 1.0 = mỗi giây (/s), 60.0 = mỗi phút (/min), 3600.0 = mỗi giờ (/h) |
| **`Cfg_HiDevLim`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho độ lệch Cao (High Deviation) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_HiDevDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng độ lệch Cao (High Deviation) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoDevLim`** | `REAL` | `-100` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho độ lệch Thấp (Low Deviation) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_LoDevDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng độ lệch Thấp (Low Deviation) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho độ lệch Cao (High Deviation): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho độ lệch Cao (High Deviation): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho độ lệch Cao (High Deviation): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho độ lệch Thấp (Low Deviation): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho độ lệch Thấp (Low Deviation): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho độ lệch Thấp (Low Deviation): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiRoCIncLim`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho tốc độ tăng Cao (High Increasing Rate-of-Change) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_HiRoCIncDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng tốc độ tăng Cao (High Increasing Rate-of-Change) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiRoCIncOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho tốc độ tăng Cao (High Increasing Rate-of-Change): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiRoCIncOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho tốc độ tăng Cao (High Increasing Rate-of-Change): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiRoCIncGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho tốc độ tăng Cao (High Increasing Rate-of-Change): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiRoCDecLim`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho tốc độ giảm Cao (High Decreasing Rate-of-Change) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_HiRoCDecDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng tốc độ giảm Cao (High Decreasing Rate-of-Change) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiRoCDecOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho tốc độ giảm Cao (High Decreasing Rate-of-Change): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiRoCDecOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho tốc độ giảm Cao (High Decreasing Rate-of-Change): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiRoCDecGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho tốc độ giảm Cao (High Decreasing Rate-of-Change): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`PSet_LowerSP`** | `REAL` | `30` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị ngưỡng đặt Dưới (Lower Setpoint, đơn vị EU) |
| **`PSet_RaiseSP`** | `REAL` | `20` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị ngưỡng đặt Trên (Raise Setpoint, đơn vị EU) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`XSet_LowerSP`** | `REAL` | `30` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị ngưỡng đặt Dưới (Lower Setpoint, đơn vị EU) |
| **`XSet_RaiseSP`** | `REAL` | `20` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị ngưỡng đặt Trên (Raise Setpoint, đơn vị EU) |
| **`PCmd_Raise`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Cưỡng bức bật ngõ ra Tăng (Out_Raise) khi đang ở chế độ Manual |
| **`PCmd_Lower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Cưỡng bức bật ngõ ra Giảm (Out_Lower) khi đang ở chế độ Manual |
| **`PCmd_None`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Tắt cả hai ngõ ra Tăng/Giảm (Out_Raise và Out_Lower) khi ở chế độ Manual |
| **`PCmd_Auto`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển vòng điều khiển sang chế độ Tự động (Automatic Loop Mode) |
| **`PCmd_Man`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển vòng điều khiển sang chế độ Bằng tay (Manual Loop Mode) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_HiDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo độ lệch Cao (High Deviation) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo độ lệch Thấp (Low Deviation) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiRoCIncAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo tốc độ tăng Cao (High Increasing Rate-of-Change) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiRoCDecAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo tốc độ giảm Cao (High Decreasing Rate-of-Change) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiDevSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo độ lệch Cao (High Deviation) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiDevUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo độ lệch Cao (High Deviation) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoDevSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo độ lệch Thấp (Low Deviation) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoDevUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo độ lệch Thấp (Low Deviation) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiRoCIncSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo tốc độ tăng Cao (High Increasing Rate-of-Change) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiRoCIncUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo tốc độ tăng Cao (High Increasing Rate-of-Change) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiRoCDecSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo tốc độ giảm Cao (High Decreasing Rate-of-Change) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiRoCDecUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo tốc độ giảm Cao (High Decreasing Rate-of-Change) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiDevUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo độ lệch Cao (High Deviation) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoDevUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo độ lệch Thấp (Low Deviation) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_HiRoCIncUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo tốc độ tăng Cao (High Increasing Rate-of-Change) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_HiRoCDecUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo tốc độ giảm Cao (High Decreasing Rate-of-Change) ngay lập tức (biến xung tự động xóa về 0) |
| **`XCmd_Raise`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Cưỡng bức bật ngõ ra Tăng (Out_Raise) khi đang ở chế độ Manual |
| **`XCmd_Lower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Cưỡng bức bật ngõ ra Giảm (Out_Lower) khi đang ở chế độ Manual |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_None`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Tắt cả hai ngõ ra Tăng/Giảm (Out_Raise và Out_Lower) khi ở chế độ Manual |
| **`XCmd_Auto`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chuyển vòng điều khiển sang chế độ Tự động (Automatic Loop Mode) |
| **`XCmd_Man`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chuyển vòng điều khiển sang chế độ Bằng tay (Manual Loop Mode) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |

## 3. Bảng chi tiết 82 Biến Đầu Ra (`Output Parameters`) của `P_DBC`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_Raise`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển tác động làm Tăng giá trị biến quá trình PV |
| **`Out_Lower`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển tác động làm Giảm giá trị biến quá trình PV |
| **`Out_Q`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra chốt Flip-Flop thuận: Bật lên 1 khi vượt ngưỡng Raise SP và Tắt về 0 khi xuống dưới ngưỡng Lower SP |
| **`Out_Qnot`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra chốt Flip-Flop đảo: Bật lên 1 khi xuống dưới ngưỡng Lower SP và Tắt về 0 khi vượt ngưỡng Raise SP |
| **`Val_PV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến sơ cấp kỹ thuật số (HART Primary Variable - PV, tính theo đơn vị PVEU) |
| **`Val_RoC`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tốc độ biến thiên tức thời của giá trị PV (Rate of Change, tính theo đơn vị EU trên một đơn vị thời gian Cfg_RateTime) |
| **`Val_LowerSP`** | `REAL` | `30` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị ngưỡng đặt Dưới (Lower Setpoint, đơn vị EU) đang được chấp nhận áp dụng |
| **`Val_RaiseSP`** | `REAL` | `20` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị ngưỡng đặt Trên (Raise Setpoint, đơn vị EU) đang được chấp nhận áp dụng |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đo đơn vị kỹ thuật PV = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đo đơn vị kỹ thuật PV = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái xác nhận của bộ điều khiển vùng trễ: 0 = Out_Q đang Tắt, 1 = Out_Q đang Bật, 2 = Đang bật Out_Raise, 3 = Đang bật Out_Lower (4..7 = tương tự khi ở chế độ Manual) |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Raise`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV vượt ngưỡng tác động, ngõ ra Tăng (Out_Raise) đang BẬT |
| **`Sts_Lower`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV vượt ngưỡng tác động, ngõ ra Giảm (Out_Lower) đang BẬT |
| **`Sts_Auto`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Vòng điều khiển đang ở chế độ Tự động (Automatic Loop Mode) |
| **`Sts_Man`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Vòng điều khiển đang ở chế độ Bằng tay (Manual Loop Mode) |
| **`Sts_Q`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra chốt Out_Q đang BẬT (và Out_Qnot đang TẮT) |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Rate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Cơ sở thời gian tính tốc độ biến thiên (Cfg_RateTime) không hợp lệ (dùng 1.0=giây, 60.0=phút, 3600.0=giờ, 86400.0=ngày) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
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
| **`Sts_HiDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của độ lệch Cao (High Deviation) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của độ lệch Cao (High Deviation) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiDev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện độ lệch Cao (High Deviation) |
| **`Alm_HiDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho độ lệch Cao (High Deviation) |
| **`Ack_HiDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động độ lệch Cao (High Deviation) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Cao (High Deviation) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Cao (High Deviation) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Cao (High Deviation) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của độ lệch Thấp (Low Deviation) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của độ lệch Thấp (Low Deviation) đã đếm xong, đang cho phép giám sát |
| **`Sts_LoDev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện độ lệch Thấp (Low Deviation) |
| **`Alm_LoDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho độ lệch Thấp (Low Deviation) |
| **`Ack_LoDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động độ lệch Thấp (Low Deviation) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Thấp (Low Deviation) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Thấp (Low Deviation) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Thấp (Low Deviation) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiRoCIncCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của tốc độ tăng Cao (High Increasing Rate-of-Change) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiRoCIncGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của tốc độ tăng Cao (High Increasing Rate-of-Change) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiRoCInc`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện tốc độ tăng Cao (High Increasing Rate-of-Change) |
| **`Alm_HiRoCInc`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho tốc độ tăng Cao (High Increasing Rate-of-Change) |
| **`Ack_HiRoCInc`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động tốc độ tăng Cao (High Increasing Rate-of-Change) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiRoCIncDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo tốc độ tăng Cao (High Increasing Rate-of-Change) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiRoCIncShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo tốc độ tăng Cao (High Increasing Rate-of-Change) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiRoCIncSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo tốc độ tăng Cao (High Increasing Rate-of-Change) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiRoCDecCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của tốc độ giảm Cao (High Decreasing Rate-of-Change) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiRoCDecGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của tốc độ giảm Cao (High Decreasing Rate-of-Change) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiRoCDec`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện tốc độ giảm Cao (High Decreasing Rate-of-Change) |
| **`Alm_HiRoCDec`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho tốc độ giảm Cao (High Decreasing Rate-of-Change) |
| **`Ack_HiRoCDec`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động tốc độ giảm Cao (High Decreasing Rate-of-Change) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiRoCDecDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo tốc độ giảm Cao (High Decreasing Rate-of-Change) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiRoCDecShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo tốc độ giảm Cao (High Decreasing Rate-of-Change) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiRoCDecSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo tốc độ giảm Cao (High Decreasing Rate-of-Change) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_DBC`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_DBC) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 37 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_DBC`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Deadband Controller'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'On/Off Controller'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_OutNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tên thẻ (Tagname) của khối thiết bị đầu ra đích khi nhấn nút điều hướng trên HMI |
| **`Cfg_PVEU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến sơ cấp PV hiển thị trên màn hình HMI |
| **`Cfg_PVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tên thẻ (Tagname) của khối cảm biến PV đích khi nhấn nút điều hướng PV trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_DBC'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`HiDev`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho độ lệch Cao (High Deviation) |
| **`HiDevGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho độ lệch Cao (High Deviation) |
| **`HiRoCDec`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho tốc độ giảm Cao (High Decreasing Rate-of-Change) |
| **`HiRoCDecGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho tốc độ giảm Cao (High Decreasing Rate-of-Change) |
| **`HiRoCInc`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho tốc độ tăng Cao (High Increasing Rate-of-Change) |
| **`HiRoCIncGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho tốc độ tăng Cao (High Increasing Rate-of-Change) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `??` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`LoDev`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho độ lệch Thấp (Low Deviation) |
| **`LoDevGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho độ lệch Thấp (Low Deviation) |
| **`OCmd_Auto`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Chuyển vòng điều khiển sang chế độ Tự động (Automatic Loop Mode) |
| **`OCmd_Lower`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Cưỡng bức bật ngõ ra Giảm (Out_Lower) khi đang ở chế độ Manual |
| **`OCmd_Man`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Chuyển vòng điều khiển sang chế độ Bằng tay (Manual Loop Mode) |
| **`OCmd_None`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Tắt cả hai ngõ ra Tăng/Giảm (Out_Raise và Out_Lower) khi ở chế độ Manual |
| **`OCmd_Raise`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Cưỡng bức bật ngõ ra Tăng (Out_Raise) khi đang ở chế độ Manual |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_Auto`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Auto (Chuyển vòng điều khiển sang chế độ Tự động (Automatic Loop Mode)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Lower`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Lower (Cưỡng bức bật ngõ ra Giảm (Out_Lower) khi đang ở chế độ Manual), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Man`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Man (Chuyển vòng điều khiển sang chế độ Bằng tay (Manual Loop Mode)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_None`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_None (Tắt cả hai ngõ ra Tăng/Giảm (Out_Raise và Out_Lower) khi ở chế độ Manual), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Raise`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Raise (Cưỡng bức bật ngõ ra Tăng (Out_Raise) khi đang ở chế độ Manual), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SP`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_SP (Giá trị đặt chính (Setpoint - SP, tính theo đơn vị kỹ thuật PV EU) khi chạy chế độ Tự động (Auto)), cho phép mở ô nhập số trên HMI Faceplate |
| **`OSet_LowerSP`** | `REAL` | `30` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị ngưỡng đặt Dưới (Lower Setpoint, đơn vị EU) |
| **`OSet_RaiseSP`** | `REAL` | `20` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị ngưỡng đặt Trên (Raise Setpoint, đơn vị EU) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Seq`
**Thuộc chương:** [Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)](../02_CHUONG_2_REGULATORY_PROCEDURAL_CONTROL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 96–98)  
**Tệp định nghĩa khối lệnh:** `P_Seq_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Seq`:** **100** Biến đầu vào (`Input Parameters`) | **95** Biến đầu ra (`Output Parameters`) | **94** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Seq` trong Studio 5000
- **Chức năng cốt lõi:** Bộ **Điều khiển Tuần tự theo Bước (Step Sequencer)** dựa trên mảng cấu trúc `P_SeqStep`. Mỗi bước điều khiển tối đa **32 bit DO** và **32 giá trị đặt REAL**, chờ tối đa **32 điều kiện phản hồi DI** kèm bộ đếm thời gian bước, hỗ trợ Rẽ nhánh (Jump), Tạm dừng (Hold), Tiếp tục (Resume) và nhắc lệnh người vận hành qua `P_Prompt`.
- **Khi nào CẦN sử dụng:** Khi lập trình các **quy trình chạy theo bước cố định hoặc linh hoạt** như: Chu trình rửa tự động CIP/SIP, chu trình hoàn nguyên cột lọc nước (Backwash / Regeneration), quy trình khởi động/dừng cụm máy nén hoặc pha chế mẻ nhỏ mà kỹ sư công nghệ muốn chỉnh thông số bước ngay trên HMI.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Với các nhà máy sản xuất theo mẻ lớn phức tạp chuẩn ISA-88 đa luồng song song → Dùng phần mềm chuyên dụng FactoryTalk Batch.

### Quy trình 4 bước đấu nối và cấu hình `P_Seq`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_SeqLoopCnt`, `Inp_Started`, `Inp_Branch`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_ExtInh`, `Inp_Reset`, `Inp_PromptCfm`, `Inp_PromptRdy`, `Inp_PromptSelect`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `x Ref_Steps`, `Ref_States` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasInp`, `Cfg_UseInp`, `Cfg_InpForceMask`, `Cfg_OutTrackMask`, `Cfg_OutRealTrackMask`, `Cfg_ScaleRealOut`, `Cfg_HoldOutState`, `Cfg_HoldOutMask`, `Cfg_HoldOutRealMask`, `Cfg_StopOutState`, `Cfg_StopOutMask`, `Cfg_StopOutRealMask`, `Cfg_IntlkTripAction`, `Cfg_StepFTmrDNAction`, `Cfg_QtyMin`, `Cfg_QtyMax`, `Cfg_StdQty`, `Cfg_SeqTO`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_HasInpNav`, `Cfg_HoldOutRealValue`, `Cfg_InpName`, `Cfg_InpNavTag`, `Cfg_Label`, `Cfg_OutName`, `Cfg_OutRealName`, `Cfg_QtyEU`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Seq` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Seq` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_Owner`, `PSet_Qty`, `PCmd_SeqStart`, `PCmd_SeqHold`, `PCmd_SeqRestart`, `PCmd_SeqStop`, `PCmd_SeqAbort`, `PCmd_SeqReset`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_StepTOAck`, `PCmd_SeqTOAck`, `PCmd_IntlkTripAck`, `PCmd_StepTOSuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_00…Out_31`, `Out_Real_00…Out_Real_31`, `Out_PromptReq`, `Out_PromptRef`, `Val_Qty`, `Val_QtyPct`, `Val_MaxStepIndex`, `Val_CurrStepNumAbs`, `Val_CurrStepNum`, `Val_SeqLoopCnt`, `Val_InSeqDay`, `Val_InSeqHrs`, `Val_InSeqMin`, `Val_InSeqSec`, `Val_InSeqACC`, `Val_InSeqTOPct`, `Val_InStepDay`, `Val_InStepHrs`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 100 Biến Đầu Vào (`Input Parameters`) của `P_Seq`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_SeqLoopCnt`** | `DINT` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Số lần lặp lại toàn bộ chu trình tuần tự P_Seq liên tiếp (Loop Count) |
| **`Inp_Started`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu từ logic ngoài báo cụm thiết bị đã sẵn sàng cho phép trình tự P_Seq thực thi bước chạy (1 = Ready to Execute) |
| **`Inp_Branch`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện rẽ nhánh trình tự P_Seq (1 = Thực hiện nhảy bước rẽ nhánh nếu bước hiện tại có cấu hình rẽ nhánh) |
| **`Inp_PermOK`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động có thể Bypass (1 = Tất cả điều kiện Perm OK, cho phép khởi động; thường nối từ P_Perm.Sts_PermOK) |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động KHÔNG thể Bypass (1 = Tất cả điều kiện Non-Bypassable Perm OK; thường nối từ P_Perm.Sts_NBPermOK) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Inp_PromptCfm`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu từ khối P_Prompt báo Người vận hành đã nhấn nút Xác nhận hoàn tất thao tác thủ công (1 = Operator Confirmed) |
| **`Inp_PromptRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu từ khối P_Prompt báo thông báo nhắc lệnh đã được đăng lên màn hình cho Người vận hành (1 = Prompt Posted) |
| **`Inp_PromptSelect`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Giá trị số nguyên phản hồi phương án lựa chọn của Người vận hành truyền từ khối P_Prompt về P_Seq |
| **`Cfg_HasInp`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 32-bit khai báo những chân điều kiện đầu vào nào được đấu nối sử dụng trong trình tự P_Seq |
| **`Cfg_UseInp`** | `DINT` | `2#1111_1111_1111_1111_1111_1111_1111_1111` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 32-bit cho phép nhân viên bảo trì bật/tắt việc kiểm tra từng điều kiện đầu vào của trình tự P_Seq |
| **`Cfg_InpForceMask`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 32-bit cho phép những chân điều kiện đầu vào nào được quyền cưỡng bức (Force) bởi Bảo trì |
| **`Cfg_OutTrackMask`** | `DINT` | `2#1111_1111_1111_1111_1111_1111_1111_1111` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 32-bit chọn các ngõ ra BOOL cho phép thiết bị bên dưới tự do thay đổi khi bước hiện tại không điều khiển tới chúng |
| **`Cfg_OutRealTrackMask`** | `DINT` | `2#1111_1111_1111_1111_1111_1111_1111_1111` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 32-bit chọn các ngõ ra REAL cho phép bám theo giá trị bên ngoài khi bước hiện tại không ghi giá trị mới |
| **`Cfg_ScaleRealOut`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 32-bit chọn những ngõ ra REAL nào sẽ được nhân tỷ lệ phần trăm theo chân Inp_ScalePct |
| **`Cfg_HoldOutState`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Trạng thái (0 hoặc 1) xuất ra các chân BOOL tương ứng khi trình tự ở trạng thái Tạm giữ (Hold) |
| **`Cfg_HoldOutMask`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 32-bit chọn những ngõ ra BOOL nào sẽ bị ghi đè giá trị an toàn khi trình tự chuyển sang Tạm giữ (Hold) |
| **`Cfg_HoldOutRealMask`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 32-bit chọn những ngõ ra số thực REAL nào sẽ bị ghi đè khi trình tự chuyển sang Tạm giữ (Hold) |
| **`Cfg_StopOutState`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Trạng thái (0 hoặc 1) xuất ra các chân BOOL tương ứng khi trình tự ở trạng thái Dừng (Stop) |
| **`Cfg_StopOutMask`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 32-bit chọn những ngõ ra BOOL nào sẽ bị ghi đè khi trình tự chuyển sang Dừng (Stop) |
| **`Cfg_StopOutRealMask`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 32-bit chọn những ngõ ra số thực REAL nào sẽ bị ghi đè khi trình tự chuyển sang Dừng (Stop) |
| **`Cfg_IntlkTripAction`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý của trình tự P_Seq khi vi phạm Khóa liên động (Interlock Trip): 0 = Chuyển sang Dừng (Stop), 1 = Chuyển sang Tạm giữ (Hold), 2 = Nhảy tới bước xử lý liên động chỉ định |
| **`Cfg_StepFTmrDNAction`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý khi một bước trong trình tự P_Seq bị quá thời gian (Step Timeout): 0 = Chỉ bật cờ trạng thái và báo động, 1 = Tự động chuyển trình tự sang trạng thái Tạm giữ an toàn (Hold) |
| **`Cfg_QtyMin`** | `INT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn dưới của tín hiệu đo khối lượng/thể tích đầu vào |
| **`Cfg_QtyMax`** | `INT` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn trên của tín hiệu đo khối lượng/thể tích đầu vào |
| **`Cfg_StdQty`** | `INT` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Khối lượng/thể tích mẻ định mức chuẩn (Standard Nominal Quantity = 100%) |
| **`Cfg_SeqTO`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tối đa cho phép để hoàn thành toàn bộ trình tự P_Seq trước khi báo lỗi quá thời gian Sequence Timeout (mili-giây) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasPrompt`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết trình tự P_Seq này có liên kết với khối Nhắc lệnh thủ công (P_Prompt) |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Điều kiện cho phép (P_Perm) nối vào chân Inp_PermOK và bật nút điều hướng mở Faceplate P_Perm |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasStepTOAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo quá thời gian thực thi bước hiện tại (Step Timeout): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasSeqTOAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_StepTOResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo quá thời gian thực thi bước hiện tại (Step Timeout): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_SeqTOResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_StepTOAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo quá thời gian thực thi bước hiện tại (Step Timeout): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_SeqTOAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_SeqTOSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_StepTOSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo quá thời gian thực thi bước hiện tại (Step Timeout) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`PSet_Qty`** | `INT` | `100` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Tổng khối lượng / thể tích mẻ nguyên liệu cần nạp (Target Quantity, đơn vị EU) |
| **`XSet_Qty`** | `INT` | `100` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Tổng khối lượng / thể tích mẻ nguyên liệu cần nạp (Target Quantity, đơn vị EU) |
| **`MSet_SingleStep`** | `BOOL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Chế độ chạy từng bước đơn (Single Step) trong Bảo trì: 0 = Chạy liên tục bình thường, 1 = Dừng lại sau mỗi bước |
| **`PCmd_SeqStart`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Bắt đầu chạy trình tự điều khiển tuần tự (Start Sequence) |
| **`PCmd_SeqHold`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Tạm giữ an toàn trình tự (Hold Sequence) |
| **`PCmd_SeqRestart`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động tiếp tục trình tự từ trạng thái tạm giữ (Restart Sequence) |
| **`PCmd_SeqStop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng trình tự điều khiển tuần tự (Stop Sequence) |
| **`PCmd_SeqAbort`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Hủy bỏ khẩn cấp trình tự (Abort Sequence) |
| **`PCmd_SeqReset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đặt lại trình tự về trạng thái nghỉ ban đầu Idle (Reset Sequence) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_StepTOAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo quá thời gian thực thi bước hiện tại (Step Timeout) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_SeqTOAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_StepTOSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo quá thời gian thực thi bước hiện tại (Step Timeout) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_SeqTOSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_StepTOUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo quá thời gian thực thi bước hiện tại (Step Timeout) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_SeqTOUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_StepTOUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo quá thời gian thực thi bước hiện tại (Step Timeout) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_SeqTOUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_SeqStart`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Bắt đầu chạy trình tự điều khiển tuần tự (Start Sequence) |
| **`OCmd_SeqHold`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Tạm giữ an toàn trình tự (Hold Sequence) |
| **`OCmd_SeqRestart`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động tiếp tục trình tự từ trạng thái tạm giữ (Restart Sequence) |
| **`OCmd_SeqStop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng trình tự điều khiển tuần tự (Stop Sequence) |
| **`OCmd_SeqAbort`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Hủy bỏ khẩn cấp trình tự (Abort Sequence) |
| **`OCmd_SeqReset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Đặt lại trình tự về trạng thái nghỉ ban đầu Idle (Reset Sequence) |
| **`OCmd_SeqPause`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Tạm dừng bước hiện tại của trình tự (Pause Sequence) |
| **`OCmd_SeqResume`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Tiếp tục chạy trình tự sau khi tạm dừng (Resume Sequence) |
| **`OCmd_SeqAutoPause`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Đặt chế độ tự động tạm dừng khi kết thúc bước hiện tại (Auto-Pause Sequence) |
| **`OCmd_SeqCancelPause`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Hủy lệnh tạm dừng hoặc tự động tạm dừng trình tự (Cancel Pause/Auto-Pause) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_SeqStart`** | `BOOL` | `-` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Bắt đầu chạy trình tự điều khiển tuần tự (Start Sequence) |
| **`XCmd_SeqHold`** | `BOOL` | `-` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Tạm giữ an toàn trình tự (Hold Sequence) |
| **`XCmd_SeqRestart`** | `BOOL` | `-` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động tiếp tục trình tự từ trạng thái tạm giữ (Restart Sequence) |
| **`XCmd_SeqStop`** | `BOOL` | `-` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng trình tự điều khiển tuần tự (Stop Sequence) |
| **`XCmd_SeqAbort`** | `BOOL` | `-` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Hủy bỏ khẩn cấp trình tự (Abort Sequence) |
| **`XCmd_SeqReset`** | `BOOL` | `-` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Đặt lại trình tự về trạng thái nghỉ ban đầu Idle (Reset Sequence) |
| **`XCmd_SeqPause`** | `BOOL` | `-` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Tạm dừng bước hiện tại của trình tự (Pause Sequence) |
| **`XCmd_SeqResume`** | `BOOL` | `-` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Tiếp tục chạy trình tự sau khi tạm dừng (Resume Sequence) |
| **`XCmd_SeqAutoPause`** | `BOOL` | `-` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Đặt chế độ tự động tạm dừng khi kết thúc bước hiện tại (Auto-Pause Sequence) |
| **`XCmd_SeqCancelPause`** | `BOOL` | `-` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Hủy lệnh tạm dừng hoặc tự động tạm dừng trình tự (Cancel Pause/Auto-Pause) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 95 Biến Đầu Ra (`Output Parameters`) của `P_Seq`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `-` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_00…Out_31`** | `BOOL` | `-` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 32 bit ngõ ra điều khiển rời rạc của bộ điều khiển tuần tự (Sequencer Discrete Outputs 00..31) |
| **`Out_Real_00…Out_Real_31`** | `REAL` | `-` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 32 giá trị thực (REAL) xuất ra ở từng bước của bộ điều khiển tuần tự (Sequencer Real Outputs 00..31) |
| **`Out_PromptReq`** | `BOOL` | `-` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ yêu cầu hiển thị thông báo nhắc lệnh thủ công cho Người vận hành (1 = Yêu cầu hiển thị Prompt) |
| **`Out_PromptRef`** | `DINT` | `-` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Mã số tham chiếu chỉ định thông báo nhắc lệnh nào trong mảng sẽ được hiển thị |
| **`Val_Qty`** | `INT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng khối lượng/thể tích mẻ nguyên liệu đặt trước đang được chấp nhận (Accepted Target Quantity, đơn vị EU) |
| **`Val_QtyPct`** | `REAL` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Phần trăm khối lượng/thể tích đã nạp so với giá trị định mức chuẩn (% Nominal Quantity) |
| **`Val_MaxStepIndex`** | `DINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Chỉ số bước lớn nhất cho phép (= Kích thước mảng Ref_Steps - 1) |
| **`Val_CurrStepNumAbs`** | `DINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Chỉ số tuyệt đối trong mảng Ref_Steps của bước trình tự đang thực thi hiện tại |
| **`Val_CurrStepNum`** | `DINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số thứ tự bước đang thực thi hiện tại của trình tự P_Seq (tương đối trong nhánh/trạng thái) |
| **`Val_SeqLoopCnt`** | `DINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số vòng lặp toàn trình tự P_Seq đã hoàn thành |
| **`Val_InSeqDay`** | `INT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số ngày đã trôi qua kể từ khi bắt đầu chạy trình tự (trong định dạng ddd hh:mm:ss) |
| **`Val_InSeqHrs`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giờ đã trôi qua kể từ khi bắt đầu chạy trình tự (trong định dạng ddd hh:mm:ss) |
| **`Val_InSeqMin`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số phút đã trôi qua kể từ khi bắt đầu chạy trình tự (trong định dạng ddd hh:mm:ss) |
| **`Val_InSeqSec`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giây đã trôi qua kể từ khi bắt đầu chạy trình tự (trong định dạng ddd hh:mm:ss) |
| **`Val_InSeqACC`** | `DINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian tích lũy trong bộ đếm thời gian tổng của trình tự (mili-giây) |
| **`Val_InSeqTOPct`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Phần trăm thời gian đã trôi qua so với ngưỡng quá thời gian toàn trình tự (Sequence Timeout %) |
| **`Val_InStepDay`** | `INT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số ngày đã ở trong bước hiện tại (trong định dạng ddd hh:mm:ss) |
| **`Val_InStepHrs`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giờ đã ở trong bước hiện tại (trong định dạng hh:mm:ss) |
| **`Val_InStepMin`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số phút đã ở trong bước hiện tại (trong định dạng hh:mm:ss) |
| **`Val_InStepSec`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giây đã ở trong bước hiện tại (trong định dạng hh:mm:ss) |
| **`Val_InStepACC`** | `DINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian tích lũy trong bộ đếm thời gian của bước hiện tại (mili-giây) |
| **`Val_InStepTOPct`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Phần trăm thời gian đã trôi qua so với ngưỡng quá thời gian của bước hiện tại (Step Timeout %) |
| **`Val_StepTRemainHrs`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giờ còn lại của bộ định thời bước hiện tại (trong định dạng hh:mm:ss) |
| **`Val_StepTRemainMin`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số phút còn lại của bộ định thời bước hiện tại (trong định dạng hh:mm:ss) |
| **`Val_StepTRemainSec`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giây còn lại của bộ định thời bước hiện tại (trong định dạng hh:mm:ss) |
| **`Val_StepTRemain`** | `DINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian đếm ngược còn lại của bước hiện tại trước khi cho phép chuyển sang bước tiếp theo |
| **`Val_State`** | `DINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái máy trạng thái nội bộ (Internal Logic State) dùng để tạo hiệu ứng chuyển động sơ đồ trạng thái trên HMI Faceplate |
| **`Val_Owner`** | `DINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Val_NotifyAll`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_SeqIdle`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trình tự điều khiển tuần tự P_Seq đang ở trạng thái Nghỉ ban đầu (Idle — sẵn sàng nhận lệnh Start) |
| **`Sts_SeqStarting`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trình tự P_Seq đang trong giai đoạn Khởi động (Starting) |
| **`Sts_SeqRunning`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trình tự P_Seq đang trong giai đoạn Chạy thực thi các bước (Running) |
| **`Sts_SeqHeld`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trình tự P_Seq đang ở trạng thái Tạm giữ an toàn (Held) |
| **`Sts_SeqComplete`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trình tự P_Seq đã hoàn thành toàn bộ các bước (Complete) |
| **`Sts_SeqStopping`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trình tự P_Seq đang trong quá trình Dừng (Stopping) |
| **`Sts_SeqStopped`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trình tự P_Seq đã Dừng hoàn toàn (Stopped — cần lệnh Reset để về Idle) |
| **`Sts_SeqPaused`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trình tự P_Seq đang Tạm dừng tại bước hiện tại (Paused) |
| **`Sts_SeqExecuting`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trình tự P_Seq đang hoạt động (đang Starting, Running hoặc Holding) |
| **`Sts_InpForced`** | `DINT` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit (32-bit) cho biết những điều kiện đầu vào nào của bước hiện tại đang bị Bảo trì cưỡng bức (Forced) |
| **`Sts_Qualified`** | `DINT` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit (32-bit) cho biết những điều kiện đầu vào nào của bước hiện tại đã thỏa mãn trạng thái yêu cầu |
| **`Sts_TQualified`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời gian của bước hiện tại đã đếm xong, đủ điều kiện thời gian để chuyển bước |
| **`Sts_MQualified`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thông báo nhắc lệnh thủ công (Manual Prompt) của bước hiện tại đã được xác nhận xong |
| **`Sts_Available`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Intlk`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_Perm`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_OoS`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Sts_MaintByp`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Alarm`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_StepSizeFault`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Số lượng phần tử trong mảng bước trình tự (Ref_Steps) không hợp lệ |
| **`Err_NextStep`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Số thứ tự bước tiếp theo (Next Step Number) nằm ngoài phạm vi mảng bước trình tự |
| **`Err_QtyCfg`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Các ngưỡng khối lượng Min, Max hoặc Định mức chuẩn (Std Quantity) cài đặt không hợp lệ |
| **`Err_Qty`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Khối lượng/lưu lượng mẻ nhập vào vượt ngoài giới hạn [Cfg_QtyMin .. Cfg_QtyMax] |
| **`Sts_OoS`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Ngưng phục vụ (Out of Service — ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Bảo trì (Maintenance — ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển ngoài (External — ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động (Program) |
| **`Sts_ProgLocked`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động và đã Khóa (Program Locked — không cho Operator lấy quyền) |
| **`Sts_Oper`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành trên HMI (Operator) |
| **`Sts_OperLocked`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành và đã Khóa (Operator Locked — không cho Program lấy quyền) |
| **`Sts_Normal`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn lệnh hiện hành đang khớp với nguồn lệnh Bình thường mặc định (Program hoặc Operator theo Cfg_ProgNormal) |
| **`Sts_ExtReqInh`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ External đang bị khóa cấm (không thể chuyển sang External từ trạng thái hiện tại) |
| **`Sts_ProgReqInh`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ Program đang bị khóa cấm (không thể chuyển sang Program từ trạng thái hiện tại) |
| **`Sts_MAcqRcvd`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã nhận được lệnh Chiếm quyền bảo trì (MCmd_Acq) trong chu kỳ quét hiện tại |
| **`Sts_RdyReset`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_StepTO`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện quá thời gian thực thi bước hiện tại (Step Timeout) |
| **`Alm_StepTO`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho quá thời gian thực thi bước hiện tại (Step Timeout) |
| **`Ack_StepTO`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động quá thời gian thực thi bước hiện tại (Step Timeout) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_StepTODisabled`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo quá thời gian thực thi bước hiện tại (Step Timeout) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_StepTOShelved`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo quá thời gian thực thi bước hiện tại (Step Timeout) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_StepTOSuppressed`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo quá thời gian thực thi bước hiện tại (Step Timeout) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_SeqTO`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout) |
| **`Alm_SeqTO`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout) |
| **`Ack_SeqTO`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_SeqTODisabled`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_SeqTOShelved`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_SeqTOSuppressed`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`x Ref_Steps`** | `P_SeqStep[2]` | `-` | Ẩn (Tag) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng chứa danh sách cấu hình toàn bộ các bước (Steps) của trình tự P_Seq |
| **`P_Seq`** | `BOOL` | `-` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Seq) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 94 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Seq`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Simple Sequencer'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_HasInpNav`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | [Biến cấu hình thông số] Từ bit (Bitmask) bật nút điều hướng trên HMI cho từng kênh đầu vào tương ứng (Bit = 1 là bật) |
| **`Cfg_HoldOutRealValue`** | `REAL[32]` | `{...}` | [Biến cấu hình thông số] Mảng 32 giá trị số thực REAL xuất ra khi trình tự ở trạng thái Tạm giữ (Hold) |
| **`Cfg_InpName`** | `STRING_20[32]` | `{...}` | [Biến cấu hình thông số] Mảng tên mô tả của 32 điều kiện đầu vào hiển thị trên giao diện trình tự P_Seq |
| **`Cfg_InpNavTag`** | `STRING_NavTag[32]` | `{...}` | [Biến cấu hình thông số] Mảng tên thẻ (Tagname) đích cho các nút điều hướng đầu vào trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Simple Sequencer'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_OutName`** | `STRING_20[32]` | `{...}` | [Biến cấu hình thông số] Mảng tên mô tả của 32 ngõ ra rời rạc (BOOL) hiển thị trên giao diện trình tự P_Seq |
| **`Cfg_OutRealName`** | `STRING_20[32]` | `{...}` | [Biến cấu hình thông số] Mảng tên mô tả của 32 ngõ ra số thực (REAL) hiển thị trên giao diện trình tự P_Seq |
| **`Cfg_QtyEU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tổng khối lượng/thể tích mẻ định lượng (ví dụ: "kg", "L") hiển thị trên HMI |
| **`Cfg_StopOutRealValue`** | `REAL[32]` | `{...}` | [Biến cấu hình thông số] Mảng 32 giá trị số thực REAL xuất ra khi trình tự ở trạng thái Dừng (Stop) |
| **`Cfg_Tag`** | `STRING_20` | `'P_Seq'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`Cur_EditState`** | `DINT` | `0` | Biến nội bộ lưu nhánh trạng thái của trình tự P_Seq đang được mở chỉnh sửa |
| **`Cur_EditStep`** | `DINT` | `0` | Cấu trúc dữ liệu lưu thông tin của bước trình tự P_Seq đang được mở chỉnh sửa |
| **`Cur_EditStepRel`** | `DINT` | `0` | Số thứ tự tương đối của bước trình tự P_Seq đang được mở chỉnh sửa |
| **`Cur_HasFaultTimer`** | `BOOL` | `0` | Cờ nội bộ báo bước trình tự hiện tại có bật bộ định thời báo lỗi quá thời gian (Step Timeout) |
| **`Cur_HasTimer`** | `BOOL` | `0` | Cờ nội bộ báo bước trình tự hiện tại có sử dụng bộ định thời chuyển bước |
| **`Cur_Step`** | `P_SeqStep` | `{...}` | Cấu trúc dữ liệu lưu toàn bộ thông số của bước trình tự P_Seq đang thực thi hiện tại |
| **`Cur_StepHasInp`** | `DINT` | `0` | Cờ nội bộ báo bước trình tự hiện tại có kiểm tra điều kiện đầu vào để chuyển bước |
| **`Cur_StepQualifyState`** | `DINT` | `0` | Trạng thái đánh giá điều kiện chuyển bước của bước trình tự hiện tại |
| **`Cur_TimerCfg`** | `BOOL` | `0` | Cấu hình bộ định thời của bước trình tự hiện tại |
| **`HMI_CurrentPosition`** | `DINT` | `0` | Biến nội bộ HMI lưu vị trí cuộn trang danh sách bước trình tự P_Seq hiện tại |
| **`HMI_EDitStepAbs`** | `DINT` | `0` | Biến nội bộ HMI lưu chỉ số tuyệt đối của bước trình tự đang chỉnh sửa |
| **`HMI_EDitStepNextAbs`** | `DINT` | `0` | Biến nội bộ HMI lưu chỉ số tuyệt đối của bước trình tự kế tiếp khi chỉnh sửa |
| **`HMI_EDitStepPrevAbs`** | `DINT` | `0` | Biến nội bộ HMI lưu chỉ số tuyệt đối của bước trình tự liền trước khi chỉnh sửa |
| **`HMI_HasSeqTO`** | `BOOL` | `0` | Biến nội bộ HMI bật/tắt giám sát quá thời gian toàn trình tự |
| **`HMI_HasStepTO`** | `BOOL` | `0` | Biến nội bộ HMI bật/tắt giám sát quá thời gian bước hiện tại |
| **`HMI_InMask`** | `DINT[11]` | `{...}` | Biến nội bộ HMI lưu mặt nạ các đầu vào sử dụng trong bước trình tự |
| **`HMI_InState`** | `DINT[11]` | `{...}` | Biến nội bộ HMI lưu trạng thái yêu cầu của các đầu vào trong bước trình tự |
| **`HMI_InStepNum`** | `DINT` | `0` | Biến nội bộ HMI lưu số thứ tự bước trình tự đang xem chi tiết |
| **`HMI_InStepVis`** | `DINT` | `0` | Biến nội bộ HMI điều khiển ẩn/hiện trang thông tin bước trình tự |
| **`HMI_Lib`** | `STRING_16` | `'RA-Seq'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Manual`** | `DINT` | `0` | Biến nội bộ HMI cấu hình thông báo nhắc lệnh thủ công cho bước trình tự |
| **`HMI_MaxCntRealOut`** | `DINT` | `0` | Biến nội bộ HMI lưu số lượng ngõ ra số thực tối đa hiển thị trên trang |
| **`HMI_NumStepsInEditState`** | `DINT` | `0` | Biến nội bộ HMI lưu tổng số bước trong nhánh trạng thái đang chỉnh sửa |
| **`HMI_NumStepsInState`** | `DINT` | `0` | Biến nội bộ HMI lưu tổng số bước trong nhánh trạng thái đang chạy |
| **`HMI_Pause`** | `DINT` | `0` | Biến nội bộ HMI điều khiển tạm dừng tại bước trình tự |
| **`HMI_QtyVis`** | `BOOL` | `0` | Biến nội bộ HMI điều khiển hiển thị thông số định lượng trên bước trình tự |
| **`HMI_RealOutVis`** | `DINT` | `0` | Biến nội bộ HMI điều khiển hiển thị các ngõ ra số thực của bước trình tự |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_TimerDeferred`** | `DINT` | `0` | Biến nội bộ HMI cấu hình trì hoãn bộ đếm thời gian bước |
| **`HMI_TimerType`** | `DINT` | `0` | Biến nội bộ HMI chọn kiểu bộ đếm thời gian của bước trình tự |
| **`HMI_TimerUsed`** | `DINT` | `0` | Biến nội bộ HMI bật sử dụng bộ đếm thời gian trong bước trình tự |
| **`HMI_Type`** | `STRING_16` | `'P_Seq'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`HMI_ViewFirstStep`** | `DINT` | `0` | Biến nội bộ HMI nhảy về xem bước đầu tiên của trình tự |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MCmd_InpForce`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Cưỡng bức trạng thái điều kiện đầu vào của bước hiện tại (Force Step Input per bit) |
| **`MCmd_SeqStepForce`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Cưỡng bức chuyển sang bước tiếp theo của trình tự (Force Step Transition) |
| **`MCmd_TimerForce`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Cưỡng bức hoàn thành ngay bộ định thời của bước hiện tại (Force Step Timer Done) |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_NextStep`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt MSet_NextStep (Số thứ tự bước tiếp theo sẽ thực thi khi cưỡng bức chuyển bước trong chế độ Bảo trì (Maintenance)), cho phép mở ô nhập số trên HMI Faceplate |
| **`MRdy_SeqStepForce`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SeqStepForce (Cưỡng bức chuyển sang bước tiếp theo của trình tự (Force Step Transition)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MSet_NextStep`** | `DINT` | `0` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Số thứ tự bước tiếp theo sẽ thực thi khi cưỡng bức chuyển bước trong chế độ Bảo trì (Maintenance) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_StdQtyReset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khôi phục lượng đặt mẻ (OSet_Qty) về giá trị định mức chuẩn (Cfg_StdQty) |
| **`OCmd_StepAdd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Thêm một bước mới vào cuối trình tự (Add Step) |
| **`OCmd_StepDel`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa bước chỉ định khỏi trình tự (Delete Step) |
| **`OCmd_StepIns`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Chèn một bước mới vào vị trí chỉ định trong trình tự (Insert Step) |
| **`ORdy_AddStep`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_AddStep (Cho phép thêm bước mới vào trình tự trên HMI), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_DeleteStep`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_DeleteStep (Cho phép xóa bước khỏi trình tự trên HMI), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Edit`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Edit (Mở chế độ chỉnh sửa thông số bước trình tự trên HMI (Edit Sequence)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_InsertStep`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_InsertStep (Cho phép chèn bước mới vào trình tự trên HMI), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Output`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | [Cờ cho phép sáng nút nhấn trên HMI] Từ bit (32-bit) cho phép Người vận hành điều khiển thủ công các ngõ ra rời rạc BOOL của bước trình tự trên HMI |
| **`ORdy_Qty`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_Qty (Tổng khối lượng / thể tích mẻ nguyên liệu cần nạp (Target Quantity, đơn vị EU)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_RealOutput`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | [Cờ cho phép nhập dữ liệu trên HMI] Từ bit (32-bit) cho phép Người vận hành nhập thủ công các ngõ ra số thực REAL của bước trình tự trên HMI |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SeqAbort`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqAbort (Hủy bỏ khẩn cấp trình tự (Abort Sequence)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SeqAutoPause`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqAutoPause (Đặt chế độ tự động tạm dừng khi kết thúc bước hiện tại (Auto-Pause Sequence)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SeqCancelPause`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqCancelPause (Hủy lệnh tạm dừng hoặc tự động tạm dừng trình tự (Cancel Pause/Auto-Pause)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SeqHold`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqHold (Tạm giữ an toàn trình tự (Hold Sequence)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SeqPause`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqPause (Tạm dừng bước hiện tại của trình tự (Pause Sequence)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SeqReset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqReset (Đặt lại trình tự về trạng thái nghỉ ban đầu Idle (Reset Sequence)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SeqRestart`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqRestart (Khởi động tiếp tục trình tự từ trạng thái tạm giữ (Restart Sequence)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SeqResume`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqResume (Tiếp tục chạy trình tự sau khi tạm dừng (Resume Sequence)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SeqStart`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqStart (Bắt đầu chạy trình tự điều khiển tuần tự (Start Sequence)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SeqStop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqStop (Dừng trình tự điều khiển tuần tự (Stop Sequence)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_StdQtyReset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StdQtyReset (Khôi phục lượng đặt mẻ (OSet_Qty) về giá trị định mức chuẩn (Cfg_StdQty)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OSet_Qty`** | `INT` | `100` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Tổng khối lượng / thể tích mẻ nguyên liệu cần nạp (Target Quantity, đơn vị EU) |
| **`Ref_States`** | `P_SeqSt[11]` | `{...}` | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới cấu trúc dữ liệu trạng thái của trình tự P_Seq |
| **`SeqTO`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho quá thời gian thực thi toàn chuỗi trình tự (Sequence Timeout) |
| **`StepTO`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho quá thời gian thực thi bước hiện tại (Step Timeout) |
| **`Sts_QDonut`** | `BOOL` | `0` | [Cờ báo trạng thái logic (Status)] Cờ trạng thái phục vụ hiển thị vòng biểu tượng trạng thái (Donut) trên HMI |
| **`Sts_QDonutHole`** | `BOOL` | `0` | [Cờ báo trạng thái logic (Status)] Cờ trạng thái phục vụ hiển thị tâm vòng biểu tượng trạng thái (Donut Hole) trên HMI |
| **`Val_CurrStepName`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Tên mô tả của bước trình tự đang thực thi hiện tại |
| **`Val_InStepT2TO`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian còn lại trước khi bước hiện tại bị báo lỗi quá thời gian (Step Timeout Remaining Time) |
| **`Val_MinStepIndex`** | `SINT` | `1` | [Giá trị thực tế đầu ra (Logic/HMI)] Chỉ số bước nhỏ nhất hợp lệ trong mảng Ref_Steps |
| **`Val_NextStepName`** | `STRING_16` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Tên mô tả của bước trình tự tiếp theo sẽ được thực thi |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Dose`
**Thuộc chương:** [Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)](../02_CHUONG_2_REGULATORY_PROCEDURAL_CONTROL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 99–106)  
**Tệp định nghĩa khối lệnh:** `P_DoseFM / P_DoseWS`  
**Thống kê số lượng biến của `P_Dose`:** **117** Biến đầu vào (`Input Parameters`) | **95** Biến đầu ra (`Output Parameters`) | **41** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Dose` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Định lượng chiết rót chính xác (Dosing / Batch Weighing)** gồm 2 biến thể: dùng đồng hồ lưu lượng (`P_DoseFM`) hoặc dùng cân Loadcell (`P_DoseWS`). Điều khiển van nạp 2 cấp (**Nạp nhanh Bulk + Nạp tinh Dribble**), tự động đóng sớm để **bù lượng nguyên liệu còn đang rơi trên đường ống (`Pre-Act / Slop`)** và kiểm tra rò rỉ van.
- **Khi nào CẦN sử dụng:** Khi cần **nạp chính xác một lượng Kg hoặc Lít hóa chất, nước, dầu ăn, hương liệu, phụ gia vào bồn trộn** mà không cần viết logic cắt sớm phức tạp.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi chỉ cần đọc lưu lượng cộng dồn liên tục không điều khiển đóng/mở van nạp mẻ.

### Quy trình 4 bước đấu nối và cấu hình `P_Dose`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_RatePV`, `Inp_QtyPV`, `Inp_RatePVSrcQ`, `Inp_QtyPVSrcQ`, `Inp_RatePVNotify`, `Inp_QtyPVNotify`, `Inp_RunFdbk`, `Inp_DribbleFdbk`, `Inp_StopFdbk`, `Inp_CtrldEqpFault`, `Inp_RatePVBad`, `Inp_RatePVUncertain`, `Inp_QtyPVBad`, `Inp_QtyPVUncertain`, `Inp_Sim`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_LossInQty`, `Cfg_HasDribble`, `Cfg_HasEqpFdbk`, `Cfg_UseEqpFdbk`, `Cfg_AutoAdjPreact`, `Cfg_ShedOnEqpFault`, `Cfg_CalcRate`, `Cfg_CalcQty`, `Cfg_HasChanObjRate`, `Cfg_HasChanObjQty`, `Cfg_SetTrack`, `Cfg_QtyDecPlcs`, `Cfg_RateDecPlcs`, `Cfg_HasMoreObj`, `Cfg_HasOverTolAlm`, `Cfg_HasUnderTolAlm`, `Cfg_HasZeroFaultAlm`, `Cfg_HasEqpFaultAlm`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_QtyEU`, `Cfg_RateEU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Dose` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Dose` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_SP`, `PSet_DribbleQty`, `PSet_Preact`, `PSet_TolHi`, `PSet_TolLo`, `PSet_Owner`, `PCmd_ClearTot`, `PCmd_StartTot`, `PCmd_StopTot`, `PCmd_StartFlow`, `PCmd_StopFlow`, `PCmd_CheckTol`, `PCmd_Bump`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_RunFlow`, `Out_DribbleFlow`, `Out_StopFlow`, `Out_RunTot`, `Out_ClearTot`, `Val_Qty`, `Val_Rate`, `Val_Remain`, `Val_PctComplete`, `Val_SP`, `Val_DribbleQty`, `Val_Preact`, `Val_TolHi`, `Val_TolLo`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 117 Biến Đầu Vào (`Input Parameters`) của `P_Dose`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_RatePV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu lưu lượng tức thời từ lưu lượng kế (Flow Rate, đơn vị EU/thời gian theo Cfg_RateTime) |
| **`Inp_QtyPV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng khối lượng từ Cân (Weigh Scale) hoặc tổng thể tích/xung từ Lưu lượng kế (Flowmeter, đơn vị EU hoặc số xung) |
| **`Inp_RatePVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng (enum) của tín hiệu lưu lượng tức thời Inp_RatePV |
| **`Inp_QtyPVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng (enum) của tín hiệu khối lượng/thể tích Inp_QtyPV |
| **`Inp_RatePVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng đo lưu lượng Inp_RatePV |
| **`Inp_QtyPVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng đo khối lượng/thể tích Inp_QtyPV |
| **`Inp_RunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tiếp điểm phụ phản hồi trạng thái Chạy từ động cơ/khởi động từ (1 = Động cơ đang chạy, 0 = Động cơ đã dừng) |
| **`Inp_DribbleFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi xác nhận thiết bị định lượng đang chạy ở chế độ Nạp tinh tốc độ chậm (1 = Delivering at Dribble) |
| **`Inp_StopFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi xác nhận thiết bị định lượng đã Dừng hoàn toàn (1 = Confirmed Stopped) |
| **`Inp_CtrldEqpFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo lỗi từ thiết bị chấp hành định lượng (van/bơm nạp mẻ): 0 = Tốt (OK), 1 = Lỗi (Fail) |
| **`Inp_RatePVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Tín hiệu lưu lượng tức thời Inp_RatePV bị lỗi chất lượng xấu (Bad/Fail) |
| **`Inp_RatePVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Tín hiệu lưu lượng tức thời Inp_RatePV có chất lượng không chắc chắn (Uncertain) |
| **`Inp_QtyPVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Tín hiệu khối lượng/thể tích đầu vào Inp_QtyPV bị lỗi chất lượng xấu (Bad/Fail) |
| **`Inp_QtyPVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Tín hiệu khối lượng/thể tích đầu vào Inp_QtyPV có chất lượng không chắc chắn (Uncertain) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_LossInQty`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Chiều cân định lượng (Loss-in-Weight): 1 = Cân xả giảm khối lượng (xuất liệu ra khỏi bồn cân); 0 = Cân nạp tăng khối lượng (nạp liệu vào bồn cân) |
| **`Cfg_HasDribble`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng chế độ nạp 2 cấp tốc độ (chuyển sang nạp tinh nhỏ giọt Dribble ở cuối mẻ); 0 = Chỉ nạp 1 cấp tốc độ nhanh cho đến khi kết thúc |
| **`Cfg_HasEqpFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Thiết bị chấp hành định lượng có cấp tín hiệu phản hồi chạy/nạp tinh/dừng về PLC |
| **`Cfg_UseEqpFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng tín hiệu phản hồi của thiết bị định lượng để giám sát lỗi; 0 = Bỏ qua phản hồi |
| **`Cfg_AutoAdjPreact`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bật thuật toán tự động học và hiệu chỉnh lượng ngắt sớm (Auto-Adjust Preact) sau mỗi mẻ dựa trên sai số mẻ trước; 0 = Dùng Preact cố định |
| **`Cfg_ShedOnEqpFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng nạp mẻ ngay lập tức và phát báo động khi bị lỗi thiết bị (EqpFault); 0 = Chỉ phát báo động |
| **`Cfg_CalcRate`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tính đạo hàm khối lượng cân (Inp_QtyPV) theo thời gian để suy ra lưu lượng tức thời; 0 = Đọc trực tiếp lưu lượng từ Inp_RatePV |
| **`Cfg_CalcQty`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tính tích phân lưu lượng (Inp_RatePV) theo thời gian để suy ra tổng khối lượng/thể tích mẻ; 0 = Đọc trực tiếp tổng lượng từ Inp_QtyPV |
| **`Cfg_HasChanObjRate`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối kênh (P_AIChan) liên kết với tín hiệu lưu lượng Inp_RatePV |
| **`Cfg_HasChanObjQty`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối kênh (P_AIChan) liên kết với tín hiệu khối lượng Inp_QtyPV |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_QtyDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho khối lượng/thể tích mẻ trên HMI (từ 0 đến 6 chữ số) |
| **`Cfg_RateDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho lưu lượng nạp tức thời trên HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasOverTolAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasUnderTolAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasZeroFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasEqpFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_OverTolResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_UnderTolResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_ZeroFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_EqpFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_OverTolAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_UnderTolAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_ZeroFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_EqpFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_OverTolSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_UnderTolSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_ZeroFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_EqpFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MaxQty`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Khối lượng/thể tích mẻ tối đa cho phép cài đặt (Maximum Target Quantity, đơn vị EU) |
| **`Cfg_EUQtyMult`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số nhân quy đổi đơn vị từ Lưu lượng (Rate EU) sang Tổng lượng (Quantity EU) |
| **`Cfg_Rollover`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị tràn bộ đếm xung (Rollover Count) của đầu vào Inp_QtyPV khi dùng bộ đếm xung tốc độ cao |
| **`Cfg_CountsPerEU`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số xung trên mỗi đơn vị kỹ thuật (Counts per EU — số xung từ đồng hồ lưu lượng tương ứng với 1.0 đơn vị thể tích/khối lượng) |
| **`Cfg_RateTime`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Đơn vị cơ sở thời gian để tính tốc độ biến thiên (Rate of Change): 1.0 = mỗi giây (/s), 60.0 = mỗi phút (/min), 3600.0 = mỗi giờ (/h) |
| **`Cfg_RateFiltTC`** | `REAL` | `0.1` | Ẩn (Tag) | [Biến cấu hình thông số] Hằng số thời gian lọc thông thấp cho giá trị lưu lượng tính toán (giây; 0.0 = không lọc) |
| **`Cfg_LoRateCutoff`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng cắt lưu lượng nhỏ (Low Flow/Rate Cutoff, đơn vị EU): Khi tín hiệu thấp hơn ngưỡng này sẽ được ép về 0.0 để chống trôi bộ đếm tổng |
| **`Cfg_SettleT`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ dòng chảy lắng dịu hoàn toàn sau khi đóng van trước khi chốt khối lượng cuối cùng để kiểm tra dung sai (giây) |
| **`Cfg_BumpT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng thời gian mở van nhấp bù thêm nguyên liệu (Bump Time, tính bằng giây) khi mẻ bị thiếu |
| **`Cfg_AutoAdjPct`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Phần trăm sai số của mẻ vừa nạp được trích ra để cộng/trừ tự động vào giá trị Preact cho mẻ kế tiếp (%) |
| **`Cfg_SimRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Lưu lượng nạp nhanh giả lập khi chạy ở chế độ Mô phỏng (Inp_Sim = 1, đơn vị EU/thời gian) |
| **`Cfg_SimDribbleRate`** | `REAL` | `0.1` | Ẩn (Tag) | [Biến cấu hình thông số] Lưu lượng nạp tinh nhỏ giọt giả lập khi chạy ở chế độ Mô phỏng (Inp_Sim = 1, đơn vị EU/thời gian) |
| **`Cfg_ClearPulseT`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng xung xuất ra chân Out_ClearTot để xóa bộ đếm tổng bên ngoài (giây) |
| **`Cfg_FaultT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa để phản hồi thiết bị định lượng khớp với lệnh xuất ra trước khi báo lỗi EqpFault (giây) |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Operator giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Program (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Program giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Operator (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép External giữ quyền điều khiển khi ở Program/Operator (.0 = Giữ tốc độ đặt Reference, .1 = Giữ lệnh điều khiển Commands) |
| **`PSet_SP`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị đặt chính (Setpoint - SP, tính theo đơn vị kỹ thuật PV EU) khi chạy chế độ Tự động (Auto) |
| **`PSet_DribbleQty`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Lượng nguyên liệu chuyển sang chạy nạp tinh tốc độ chậm ở cuối mẻ (Dribble Quantity, đơn vị EU) |
| **`PSet_Preact`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Lượng nguyên liệu ngắt sớm trước khi đạt tổng mẻ để bù lượng còn rơi trên đường ống (Pre-Act, đơn vị EU) |
| **`PSet_TolHi`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giới hạn dung sai trên cho phép vượt quá giá trị đặt SP của mẻ nạp (High Tolerance Limit, đơn vị EU) |
| **`PSet_TolLo`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giới hạn dung sai dưới cho phép thấp hơn giá trị đặt SP của mẻ nạp (Low Tolerance Limit, đơn vị EU) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`XSet_SP`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị đặt chính (Setpoint - SP, tính theo đơn vị kỹ thuật PV EU) khi chạy chế độ Tự động (Auto) |
| **`XSet_DribbleQty`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Lượng nguyên liệu chuyển sang chạy nạp tinh tốc độ chậm ở cuối mẻ (Dribble Quantity, đơn vị EU) |
| **`XSet_Preact`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Lượng nguyên liệu ngắt sớm trước khi đạt tổng mẻ để bù lượng còn rơi trên đường ống (Pre-Act, đơn vị EU) |
| **`XSet_TolHi`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giới hạn dung sai trên cho phép vượt quá giá trị đặt SP của mẻ nạp (High Tolerance Limit, đơn vị EU) |
| **`XSet_TolLo`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giới hạn dung sai dưới cho phép thấp hơn giá trị đặt SP của mẻ nạp (Low Tolerance Limit, đơn vị EU) |
| **`PCmd_ClearTot`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa bộ đếm tổng lưu lượng/khối lượng tích lũy (Clear Totalizer) về 0 |
| **`PCmd_StartTot`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Bắt đầu chạy bộ đếm tổng tích lũy (Start Totalizer) |
| **`PCmd_StopTot`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng bộ đếm tổng tích lũy (Stop Totalizer) |
| **`PCmd_StartFlow`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Bắt đầu chu trình nạp/định lượng mẻ nguyên liệu (Start Delivery) |
| **`PCmd_StopFlow`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng hoặc tạm dừng chu trình nạp/định lượng mẻ (Stop/Pause Delivery) |
| **`PCmd_CheckTol`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Thực hiện kiểm tra dung sai mẻ nạp (Check Tolerances) |
| **`PCmd_Bump`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Nhấp bù thêm một lượng nhỏ nguyên liệu (Bump Delivery) khi mẻ nạp bị thiếu dưới dung sai |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_OverTolAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_UnderTolAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_ZeroFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_EqpFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_OverTolSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_UnderTolSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_ZeroFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_EqpFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_OverTolUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_UnderTolUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_ZeroFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_EqpFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_OverTolUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_UnderTolUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_ZeroFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_EqpFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_ClearTot`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa bộ đếm tổng lưu lượng/khối lượng tích lũy (Clear Totalizer) về 0 |
| **`XCmd_StartTot`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Bắt đầu chạy bộ đếm tổng tích lũy (Start Totalizer) |
| **`XCmd_StopTot`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng bộ đếm tổng tích lũy (Stop Totalizer) |
| **`XCmd_StartFlow`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Bắt đầu chu trình nạp/định lượng mẻ nguyên liệu (Start Delivery) |
| **`XCmd_StopFlow`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng hoặc tạm dừng chu trình nạp/định lượng mẻ (Stop/Pause Delivery) |
| **`XCmd_CheckTol`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Thực hiện kiểm tra dung sai mẻ nạp (Check Tolerances) |
| **`XCmd_Bump`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp bù thêm một lượng nhỏ nguyên liệu (Bump Delivery) khi mẻ nạp bị thiếu dưới dung sai |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 95 Biến Đầu Ra (`Output Parameters`) của `P_Dose`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `-` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_RunFlow`** | `BOOL` | `-` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị nạp mẻ ở tốc độ dòng chảy lớn (1 = Nạp nhanh Full Flow) |
| **`Out_DribbleFlow`** | `BOOL` | `-` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị nạp mẻ ở tốc độ dòng chảy nhỏ giọt (1 = Nạp tinh Dribble Flow) |
| **`Out_StopFlow`** | `BOOL` | `-` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển dừng thiết bị nạp mẻ (1 = Dừng nạp Stop Delivery) |
| **`Out_RunTot`** | `BOOL` | `-` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra cho phép bộ đếm tổng tích lũy bên ngoài hoạt động (1 = Chạy Totalizer ngoài) |
| **`Out_ClearTot`** | `BOOL` | `-` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra phát xung xóa bộ đếm tổng tích lũy bên ngoài (1 = Reset bộ đếm tổng trên lưu lượng kế/cân) |
| **`Val_Qty`** | `REAL` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng khối lượng/thể tích mẻ nguyên liệu đặt trước đang được chấp nhận (Accepted Target Quantity, đơn vị EU) |
| **`Val_Rate`** | `REAL` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Lưu lượng nạp nguyên liệu tức thời hiện tại (Current Delivery Rate, đơn vị EU/thời gian) |
| **`Val_Remain`** | `REAL` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Khối lượng/thể tích nguyên liệu còn lại cần nạp tiếp để đạt tới giá trị đặt SP (đơn vị EU) |
| **`Val_PctComplete`** | `REAL` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Phần trăm hoàn thành mẻ nạp hiện tại (0..100.00%, dùng cho thanh tiến trình trên HMI) |
| **`Val_SP`** | `REAL` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đặt Setpoint (SP) đang áp dụng thực tế cho thuật toán PID (sau khi đã Ramp và kẹp giới hạn, đơn vị PVEU) |
| **`Val_DribbleQty`** | `REAL` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Lượng nguyên liệu chuyển sang nạp tinh tốc độ chậm ở cuối mẻ đang áp dụng (đơn vị EU) |
| **`Val_Preact`** | `REAL` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Lượng nguyên liệu ngắt sớm trước khi đủ mẻ (Pre-Act / Slop) đang áp dụng (đơn vị EU) |
| **`Val_TolHi`** | `REAL` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Lượng dung sai trên cho phép vượt quá SP đang áp dụng (đơn vị EU) |
| **`Val_TolLo`** | `REAL` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Lượng dung sai dưới cho phép thấp hơn SP đang áp dụng (đơn vị EU) |
| **`SrcQ_IO`** | `SINT` | `-` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `-` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Cmd`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh định lượng hiện hành (cũng áp dụng cho Inp_OvrdCmd): 0 = Không có lệnh, 1 = Xóa bộ đếm tổng (Clear Totalizer), 2 = Chạy bộ đếm tổng (Start Totalizer), 3 = Bắt đầu nạp mẻ (Start Delivery), 4 = Chuyển sang nạp tinh (Start Dribble), 5 = Nhấp bù (Bump), 6 = Dừng nạp (Stop Flow), 7 = Dừng bộ đếm tổng (Stop Totalizer) |
| **`Val_Fdbk`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phản hồi từ thiết bị định lượng: 0 = Không có, 1 = Đang dừng (Stopped), 2 = Đang nạp nhanh (Delivering), 3 = Đang nạp tinh nhỏ giọt (Dribbling) |
| **`Val_Sts`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái xác nhận của bộ định lượng: 0 = Khởi động/Reset, 1 = Đang dừng, 2 = Đang đếm tổng, 3 = Đang nạp nhanh (Delivering), 4 = Đang nạp tinh (Dribbling), 5 = Đang nhấp bù (Bumping), 6 = Đang bắt đầu mở dòng nạp, 7 = Đang chuyển sang nạp tinh, 8 = Đang đóng ngắt dòng nạp, 33 = Ngưng phục vụ |
| **`Val_Fault`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `-` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Cleared`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã hoàn tất xóa bộ đếm tổng tích lũy (Totalizer Clear Completed) |
| **`Sts_TotRunning`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ đếm tổng tích lũy (Totalizer) đang chạy; 0 = Bộ đếm tổng đang dừng |
| **`Sts_FlowStarting`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang bắt đầu mở dòng nạp mẻ (Out_RunFlow = 1, đang chờ phản hồi chạy) |
| **`Sts_FlowRunning`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Dòng nạp nhanh (Full Flow) đang chảy ổn định (Out_RunFlow = 1 và đã có phản hồi chạy) |
| **`Sts_DribbleStarting`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang chuyển sang chế độ nạp tinh nhỏ giọt (Out_DribbleFlow = 1, đang chờ phản hồi Dribble) |
| **`Sts_FlowDribble`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang nạp tinh tốc độ chậm (Dribble Flow) ổn định ở cuối mẻ |
| **`Sts_FlowStopping`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang đóng ngắt dòng nạp mẻ (Out_StopFlow = 1, đang chờ phản hồi dừng) |
| **`Sts_FlowStopped`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Dòng nạp mẻ đã dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_Bumping`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang thực hiện nhấp bù thêm nguyên liệu (Bump Flow Active) |
| **`Sts_LoRateCutoff`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tốc độ dòng chảy / biến thiên nhỏ hơn ngưỡng cắt (Cfg_LoRateCutoff), giá trị được ép về 0 |
| **`Sts_Complete`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã nạp đủ khối lượng/thể tích mẻ mục tiêu (Tổng lượng đã nạp >= SP - Preact) |
| **`Sts_InTol`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tổng lượng nguyên liệu thực tế của mẻ nằm trong phạm vi dung sai cho phép [SP - TolLo .. SP + TolHi] |
| **`Sts_Available`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_QtyBad`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khối lượng/thể tích đo được bị lỗi chất lượng xấu (PV Fail) |
| **`Sts_QtyUncertain`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khối lượng/thể tích đo được có chất lượng không chắc chắn (Uncertain) |
| **`Sts_RateBad`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị lưu lượng tức thời đo được bị lỗi chất lượng xấu (PV Fail) |
| **`Sts_RateUncertain`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị lưu lượng tức thời đo được có chất lượng không chắc chắn (Uncertain) |
| **`Sts_NotRdy`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_PVBad`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Tín hiệu đo PV bị lỗi chất lượng xấu (Bad Quality) hoặc mất truyền thông |
| **`Nrdy_EqpFault`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị chấp hành ngoài (External Equipment Fault — cần nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Sts_MaintByp`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_EU`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Rate`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Cơ sở thời gian tính tốc độ biến thiên (Cfg_RateTime) không hợp lệ (dùng 1.0=giây, 60.0=phút, 3600.0=giờ, 86400.0=ngày) |
| **`Err_Cutoff`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Ngưỡng cắt lưu lượng nhỏ (Cfg_LoRateCutoff) không hợp lệ (nhỏ hơn 0) |
| **`Err_Limit`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Ngưỡng kẹp giới hạn trên của PV nhỏ hơn ngưỡng kẹp giới hạn dưới (Cfg_PVHiLim < Cfg_PVLoLim) |
| **`Err_Sim`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ mô phỏng (Cfg_SimFdbkT / Cfg_SimRampT) nằm ngoài phạm vi 0..2.147.483 giây |
| **`Err_Timer`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_OoS`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Ngưng phục vụ (Out of Service — ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Bảo trì (Maintenance — ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển ngoài (External — ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động (Program) |
| **`Sts_ProgLocked`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động và đã Khóa (Program Locked — không cho Operator lấy quyền) |
| **`Sts_Oper`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành trên HMI (Operator) |
| **`Sts_OperLocked`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành và đã Khóa (Operator Locked — không cho Program lấy quyền) |
| **`Sts_Normal`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn lệnh hiện hành đang khớp với nguồn lệnh Bình thường mặc định (Program hoặc Operator theo Cfg_ProgNormal) |
| **`Sts_ProgReqInh`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ Program đang bị khóa cấm (không thể chuyển sang Program từ trạng thái hiện tại) |
| **`Sts_MAcqRcvd`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã nhận được lệnh Chiếm quyền bảo trì (MCmd_Acq) trong chu kỳ quét hiện tại |
| **`Sts_RdyReset`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_OverTol`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance) |
| **`Alm_OverTol`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance) |
| **`Ack_OverTol`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_OverTolDisabled`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_OverTolShelved`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_OverTolSuppressed`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_UnderTol`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance) |
| **`Alm_UnderTol`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance) |
| **`Ack_UnderTol`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_UnderTolDisabled`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_UnderTolShelved`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_UnderTolSuppressed`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_ZeroFault`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault) |
| **`Alm_ZeroFault`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault) |
| **`Ack_ZeroFault`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_ZeroFaultDisabled`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_ZeroFaultShelved`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_ZeroFaultSuppressed`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_EqpFault`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault) |
| **`Alm_EqpFault`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault) |
| **`Ack_EqpFault`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_EqpFaultDisabled`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_EqpFaultShelved`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_EqpFaultSuppressed`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_Dose`** | `BOOL` | `-` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Dose) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 41 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Dose`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `Dosing with Weigh Scale or Flowmeter'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `Dosing'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_QtyEU`** | `STRING_8` | `'kg'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tổng khối lượng/thể tích mẻ định lượng (ví dụ: "kg", "L") hiển thị trên HMI |
| **`Cfg_RateEU`** | `STRING_8` | `'kg/sec'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của lưu lượng nạp tức thời (ví dụ: "kg/min", "L/s") hiển thị trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_Dose'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`EqpFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi thiết bị chấp hành / cảm biến định lượng (Equipment Fault) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Dose'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`OCmd_Bump`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp bù thêm một lượng nhỏ nguyên liệu (Bump Delivery) khi mẻ nạp bị thiếu dưới dung sai |
| **`OCmd_CheckTol`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Thực hiện kiểm tra dung sai mẻ nạp (Check Tolerances) |
| **`OCmd_ClearTot`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa bộ đếm tổng lưu lượng/khối lượng tích lũy (Clear Totalizer) về 0 |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_StartFlow`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Bắt đầu chu trình nạp/định lượng mẻ nguyên liệu (Start Delivery) |
| **`OCmd_StartTot`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Bắt đầu chạy bộ đếm tổng tích lũy (Start Totalizer) |
| **`OCmd_StopFlow`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng hoặc tạm dừng chu trình nạp/định lượng mẻ (Stop/Pause Delivery) |
| **`OCmd_StopTot`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng bộ đếm tổng tích lũy (Stop Totalizer) |
| **`ORdy_Bump`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Bump (Nhấp bù thêm một lượng nhỏ nguyên liệu (Bump Delivery) khi mẻ nạp bị thiếu dưới dung sai), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_CheckTol`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_CheckTol (Thực hiện kiểm tra dung sai mẻ nạp (Check Tolerances)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ClearTot`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ClearTot (Xóa bộ đếm tổng lưu lượng/khối lượng tích lũy (Clear Totalizer) về 0), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_DribPre`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_DribbleQty và OSet_Preact (cho phép mở ô nhập trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SP`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_SP (Giá trị đặt chính (Setpoint - SP, tính theo đơn vị kỹ thuật PV EU) khi chạy chế độ Tự động (Auto)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_StartFlow`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFlow (Bắt đầu chu trình nạp/định lượng mẻ nguyên liệu (Start Delivery)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_StartTot`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartTot (Bắt đầu chạy bộ đếm tổng tích lũy (Start Totalizer)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_StopFlow`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StopFlow (Dừng hoặc tạm dừng chu trình nạp/định lượng mẻ (Stop/Pause Delivery)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_StopTot`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StopTot (Dừng bộ đếm tổng tích lũy (Stop Totalizer)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Tol`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt dung sai OSet_TolHi và OSet_TolLo (cho phép mở ô nhập trên HMI Faceplate) |
| **`OSet_DribbleQty`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Lượng nguyên liệu chuyển sang chạy nạp tinh tốc độ chậm ở cuối mẻ (Dribble Quantity, đơn vị EU) |
| **`OSet_Preact`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Lượng nguyên liệu ngắt sớm trước khi đạt tổng mẻ để bù lượng còn rơi trên đường ống (Pre-Act, đơn vị EU) |
| **`OSet_SP`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị đặt chính (Setpoint - SP, tính theo đơn vị kỹ thuật PV EU) khi chạy chế độ Tự động (Auto) |
| **`OSet_TolHi`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giới hạn dung sai trên cho phép vượt quá giá trị đặt SP của mẻ nạp (High Tolerance Limit, đơn vị EU) |
| **`OSet_TolLo`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giới hạn dung sai dưới cho phép thấp hơn giá trị đặt SP của mẻ nạp (Low Tolerance Limit, đơn vị EU) |
| **`OverTol`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho khối lượng/lưu lượng mẻ vượt dung sai trên (Over Tolerance) |
| **`UnderTol`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho khối lượng/lưu lượng mẻ hụt dung sai dưới (Under Tolerance) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
| **`ZeroFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi điểm 0 không xóa được hoặc có dòng chảy bất thường (Zero Fault) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_LLS`
**Thuộc chương:** [Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)](../02_CHUONG_2_REGULATORY_PROCEDURAL_CONTROL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 107–118)  
**Tệp định nghĩa khối lệnh:** `P_LLS_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_LLS`:** **75** Biến đầu vào (`Input Parameters`) | **71** Biến đầu ra (`Output Parameters`) | **34** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_LLS` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Quản lý Nhóm Động cơ Chạy Chính / Phụ / Dự phòng (Lead / Lag / Standby Motor Group)** cho cụm từ **2 đến 30 bơm/quạt/máy nén mắc song song**. Tự động gọi chạy/dừng số lượng máy theo nhu cầu (`Demand`), tự động gọi máy dự phòng (`Standby`) vào thay thế ngay khi máy đang chạy bị Trip, và **xoay vòng thứ tự ưu tiên theo số giờ chạy tích lũy (`Wear Leveling`)**.
- **Khi nào CẦN sử dụng:** Cho **mọi trạm bơm cấp nước, trạm bơm tăng áp, dàn quạt tháp giải nhiệt (Cooling Tower), trạm máy nén khí** có từ 2 thiết bị trở lên chạy luân phiên hoặc hỗ trợ nhau theo tải.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi chỉ có 1 động cơ đơn lẻ.

### Quy trình 4 bước đấu nối và cấu hình `P_LLS`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdDemand`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_Reset`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_Motors` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_NumMotors`, `Cfg_MaxDemand`, `Cfg_MinDemand`, `Cfg_FirstOnFirstOff`, `Cfg_AllowRotate`, `Cfg_RotateOnStop`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasMoreObj`, `Cfg_HasNav`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_HasCantStartAlm`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_LLS` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_LLS` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_Demand`, `PSet_Owner`, `PCmd_Start`, `PCmd_Stop`, `PCmd_Rotate`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_CantStartAck`, `PCmd_CantStopAck`, `PCmd_IntlkTripAck`, `PCmd_CantStartSuppress`, `PCmd_CantStopSuppress`, `PCmd_IntlkTripSuppress`, `PCmd_CantStartUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val_Demand`, `Val_RotateRank`, `Val_RotateID`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Stopped`, `Sts_Running`, `Sts_Stopping`, `Sts_Increasing`, `Sts_Decreasing`, `Sts_Available`, `Sts_Bypass`, `Sts_BypActive`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 75 Biến Đầu Vào (`Input Parameters`) của `P_LLS`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Ref_Motors`** | `P_LLS_Motor[2]` | `1` | Ẩn (Tag) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng giao tiếp động cơ (Motor Interface Array liên kết từ 2 đến 30 động cơ trong cụm P_LLS) |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động có thể Bypass (1 = Tất cả điều kiện Perm OK, cho phép khởi động; thường nối từ P_Perm.Sts_PermOK) |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động KHÔNG thể Bypass (1 = Tất cả điều kiện Non-Bypassable Perm OK; thường nối từ P_Perm.Sts_NBPermOK) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdDemand`** | `DINT` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Số lượng động cơ yêu cầu chạy trong cụm khi ở chế độ Cưỡng bức (từ MinDemand đến MaxDemand) |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh điều khiển cụm động cơ khi ở chế độ Cưỡng bức (Override): 0 = Không tác động, 1 = Dừng toàn bộ cụm (Stop Group), 2 = Khởi động cụm theo nhu cầu (Start Group), 3 = Xoay vòng thứ tự ưu tiên (Rotate Assignments) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_NumMotors`** | `DINT` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Tổng số lượng động cơ thành viên được quản lý trong cụm P_LLS (từ 2 đến 30 động cơ) |
| **`Cfg_MaxDemand`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số lượng động cơ yêu cầu chạy tối đa cho phép đặt (1..Cfg_NumMotors) |
| **`Cfg_MinDemand`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Số lượng động cơ yêu cầu chạy tối thiểu cho phép đặt (0..Cfg_MaxDemand) |
| **`Cfg_FirstOnFirstOff`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Quy tắc dừng bớt máy khi giảm tải: 1 = Máy nào chạy trước thì được nghỉ trước (FIFO — cân bằng giờ chạy); 0 = Máy nào chạy sau cùng thì dừng trước (LIFO) |
| **`Cfg_AllowRotate`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng lệnh Xoay vòng thứ tự ưu tiên động cơ (Rotate / Cycle Lead) trên HMI và từ PLC |
| **`Cfg_RotateOnStop`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động xoay vòng đổi máy chạy chính (Lead) mỗi khi toàn bộ cụm động cơ dừng hết (Demand về 0) |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Điều kiện cho phép (P_Perm) nối vào chân Inp_PermOK và bật nút điều hướng mở Faceplate P_Perm |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasNav`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit (16-bit) bật nút điều hướng nhảy tới Faceplate nguyên nhân cho từng điều kiện trong 16 kênh (Bit = 1 là bật) |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép giá trị đặt của Program và Operator tự động bám theo giá trị CV khi đang ở chế độ Override hoặc Hand |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nút nhấn Dừng của Người vận hành (OCmd_Stop) có quyền ưu tiên cao nhất, bấm dừng được ngay cả khi máy đang chạy ở chế độ Program; 0 = OCmd_Stop chỉ có tác dụng khi ở chế độ Operator/Maintenance |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh Dừng từ hệ thống ngoài (XCmd_Stop) có hiệu lực ưu tiên ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_HasCantStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasCantStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_CantStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_CantStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_CantStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_CantStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_CantStartSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_CantStopSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_StartDelay`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ chờ tối thiểu giữa 2 lần gọi khởi động liên tiếp các động cơ trong cụm để tránh sụt áp lưới (giây) |
| **`Cfg_StopDelay`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ chờ tối thiểu giữa 2 lần gọi dừng liên tiếp các động cơ trong cụm để tránh búa nước (giây) |
| **`PSet_Demand`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Số lượng động cơ yêu cầu chạy trong cụm (từ Cfg_MinDemand đến Cfg_MaxDemand) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`XSet_Demand`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Số lượng động cơ yêu cầu chạy trong cụm (từ Cfg_MinDemand đến Cfg_MaxDemand) |
| **`PCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) thiết bị/động cơ |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) thiết bị/động cơ |
| **`PCmd_Rotate`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xoay vòng thứ tự ưu tiên động cơ trong nhóm (đẩy máy chạy chính Lead xuống cuối danh sách) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_CantStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_CantStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_CantStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_CantStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_CantStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_CantStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_CantStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_CantStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) thiết bị/động cơ |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) thiết bị/động cơ |
| **`XCmd_Rotate`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xoay vòng thứ tự ưu tiên động cơ trong nhóm (đẩy máy chạy chính Lead xuống cuối danh sách) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 71 Biến Đầu Ra (`Output Parameters`) của `P_LLS`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val_Demand`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số lượng động cơ trong cụm đang được yêu cầu chạy (Requested Motor Demand) |
| **`Val_RotateRank`** | `DINT` | `-1` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Thứ hạng hiện tại (0 = Lead...) của động cơ sẽ bị đẩy xuống cuối danh sách khi Xoay vòng |
| **`Val_RotateID`** | `DINT` | `-1` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số thứ tự của động cơ sẽ bị đẩy xuống cuối danh sách khi thực hiện lệnh Xoay vòng (Rotate) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh cụm động cơ hiện hành: 0 = Không tác động, 1 = Dừng cụm (Stop Group), 2 = Chạy cụm (Start Group) |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số lượng động cơ trong cụm thực tế đang chạy (từ 0 đến 30 động cơ) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái xác nhận của cụm động cơ P_LLS: 0 = Chưa xác định, 1 = Toàn cụm đang Dừng, 2 = Cụm đang Chạy đủ nhu cầu, 7 = Toàn cụm đang Dừng, 14 = Cụm đang Chạy và đang cắt bớt máy (Demand Decreasing), 15 = Cụm đang Chạy và đang gọi thêm máy (Demand Increasing), 33 = Cụm ngưng phục vụ |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đã xác nhận Dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_Running`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu chạy và đã xác nhận đang Chạy (Confirmed Running) |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đang chờ tín hiệu phản hồi về dừng (Stopping) |
| **`Sts_Increasing`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Cụm động cơ P_LLS đang trong quá trình gọi khởi động thêm máy theo trình tự để đáp ứng đủ nhu cầu (Demand) |
| **`Sts_Decreasing`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Cụm động cơ P_LLS đang trong quá trình dừng bớt máy theo trình tự để giảm về đúng nhu cầu (Demand) |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh Dừng ưu tiên từ Operator/External (cần nhấn Reset trước khi chạy lại) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_MotorAvail`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit (Bitmask) cho biết những động cơ thành viên nào trong cụm đang sẵn sàng nhận lệnh tự động (Available) |
| **`Sts_MotorStopped`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit (Bitmask) cho biết những động cơ thành viên nào trong cụm đã xác nhận Dừng (Stopped) |
| **`Sts_MotorStarting`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit (Bitmask) cho biết những động cơ thành viên nào trong cụm đang trong quá trình Khởi động (Starting) |
| **`Sts_MotorRunning`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit (Bitmask) cho biết những động cơ thành viên nào trong cụm đã xác nhận đang Chạy (Running) |
| **`Sts_MotorStopping`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit (Bitmask) cho biết những động cơ thành viên nào trong cụm đang trong quá trình Dừng (Stopping) |
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
| **`Sts_CantStart`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors) |
| **`Alm_CantStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors) |
| **`Ack_CantStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_CantStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_CantStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_CantStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_CantStop`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors) |
| **`Alm_CantStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors) |
| **`Ack_CantStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_CantStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_CantStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_CantStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_LLS`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_LLS) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 34 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_LLS`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`CantStart`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho không thể gọi chạy đủ số động cơ theo nhu cầu (Cannot Start Enough Motors) |
| **`CantStop`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho không thể dừng bớt động cơ theo nhu cầu (Cannot Stop Enough Motors) |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Lead` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Motor` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_LLS'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_LLS'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MSet_MotorOoS`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Từ bit (Bitmask) đánh dấu các động cơ trong cụm đã được Bảo trì đưa vào trạng thái Ngưng phục vụ (Out of Service) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_Rotate`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xoay vòng thứ tự ưu tiên động cơ trong nhóm (đẩy máy chạy chính Lead xuống cuối danh sách) |
| **`OCmd_Start`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) thiết bị/động cơ |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) thiết bị/động cơ |
| **`ORdy_Demand`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_Demand (Số lượng động cơ yêu cầu chạy trong cụm (từ Cfg_MinDemand đến Cfg_MaxDemand)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Rotate`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Rotate (Xoay vòng thứ tự ưu tiên động cơ trong nhóm (đẩy máy chạy chính Lead xuống cuối danh sách)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Start`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Start (Khởi động (Start) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (Dừng (Stop) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OSet_Demand`** | `DINT` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Số lượng động cơ yêu cầu chạy trong cụm (từ Cfg_MinDemand đến Cfg_MaxDemand) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
| **`Val_PrefList`** | `DINT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Mảng danh sách thứ tự ưu tiên lựa chọn động cơ của người vận hành |
| **`Val_PrioList`** | `DINT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Mảng danh sách mức độ ưu tiên của các động cơ theo thứ tự xếp hạng hiện tại |
| **`Val_RankList`** | `DINT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Mảng danh sách xếp hạng số thứ tự động cơ hiện tại: [0] = Máy chạy chính (Lead), [1] = Máy chạy phụ 1 (Lag 1)... |
| **`Val_StsList`** | `DINT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Mảng trạng thái của từng động cơ theo thứ tự xếp hạng (.0 = Sẵn sàng Available, .1 = Đang dừng Stopped, .2 = Đang khởi động Starting, .3 = Đang chạy Running) |
| **`Val_UsrList`** | `INT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Mảng danh sách tiêu chí sắp xếp tùy chỉnh (ví dụ: số giờ chạy tích lũy) theo thứ tự xếp hạng |
