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
**Thuộc chương:** Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_PIDE`:** **218** Biến đầu vào (`Input Parameters`) | **149** Biến đầu ra (`Output Parameters`) | **59** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_PIDE`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_PV`, `Inp_CascSP`, `Inp_FF`, `Inp_FFPrev`, `Inp_CVInitVal`, `Inp_CVPrev`, `Inp_UseFFPrev`, `Inp_UseCVInitVal`, `Inp_UseCVPrev`, `Inp_WindupHi`, `Inp_WindupLo`, `Inp_PVBad`, `Inp_PVUncertain`, `Inp_CVIOFault`, `Inp_PVSrcQ`, `Inp_PVNotify`, `Inp_CVNotify`, `Inp_CascSPNotify`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasMoreObj`, `Cfg_HasRatio`, `Cfg_HasCasc`, `Cfg_HasAuto`, `Cfg_HasMan`, `Cfg_HasSPRamp`, `Cfg_LockLM`, `Cfg_PVTrack`, `Cfg_CtrlAction`, `Cfg_Depend`, `Cfg_DerivSmooth`, `Cfg_ZCOff`, `Cfg_PropSPAct`, `Cfg_DerivSPAct`, `Cfg_LimitManCV`, `Cfg_InitToMan`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_CascSPNavTag`, `Cfg_CVEU`, `Cfg_CVNavTag`, `Cfg_Desc`, `Cfg_Label`, `Cfg_PVEU`, `Cfg_PVNavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_PIDE` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Ratio`, `PSet_SP`, `PSet_SPRoCLimInc`, `PSet_SPRoCLimDec`, `PSet_SPTarget`, `PSet_SPRampT`, `PSet_CV`, `PSet_Owner`, `PCmd_Casc`, `PCmd_Auto`, `PCmd_Man`, `PCmd_NormLM`, `PCmd_SPRampStart`, `PCmd_SPRampStop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_CV`, `Val_PV`, `Val_Ratio`, `Val_SPSet`, `Val_SP`, `Val_SPTarget`, `Val_SPRampT`, `Val_SPRampRoC`, `Val_SPRoCInc`, `Val_SPRoCDec`, `Val_E`, `Val_CVSet`, `Val_CVOut`, `Val_PVPercent`, `Val_SPPercent`, `Val_EPercent`, `Val_CVPercent`, `Val_ActExecT`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 218 Biến Đầu Vào (`Input Parameters`) của `P_PIDE`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_PV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input signal Process Variable (PV) from sensor (PV engineering units). |
| **`Inp_CascSP`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Setpoint in cascade, independent PV in Ratio (PV engineering units). |
| **`Inp_FF`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] FeedForward term (CVEU) |
| **`Inp_FFPrev`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] FF feedback from downstream block (CVEU) |
| **`Inp_CVInitVal`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Value to initialize the CV to when requested (CV engineering units). |
| **`Inp_CVPrev`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] CV Feedback from downstream block (CVEU) |
| **`Inp_UseFFPrev`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Use Inp_FFPrev as previous FF value, 0=Use last scan value |
| **`Inp_UseCVInitVal`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Initialize CV to Inp_CVInitValue |
| **`Inp_UseCVPrev`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Use Inp_CVPrev as previous CV value, 0=Use last scan value |
| **`Inp_WindupHi`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Windup High Signal 1=regulator will not increase the output. |
| **`Inp_WindupLo`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Windup Low Signal 1=regulator will not decrease the output. |
| **`Inp_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Bad signal quality/communication status for inputs (1 = Bad, 0 = OK). If PV is read from an analog input, then this is normally read from the analog input channel fault status. |
| **`Inp_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Uncertain quality for inputs (1 = Uncertain, 0 = OK). This is optional status for the input that can be used to drive the status of the output (Sts_PVUncertain). |
| **`Inp_CVIOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=CV I/O Comms Status Bad, 0=OK |
| **`Inp_PVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Source and Quality of Inp_PV (enum) |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] PV Alarm Level and Acknowledgement (enum) |
| **`Inp_CVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] CV Alarm Level and Acknowledgement (enum) |
| **`Inp_CascSPNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Cascase SP Alarm Level and Acknowledgement (enum) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks OK, Analog Output can be set |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Interlocks OK, Analog Output can be set |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Select Hand (hardwired) Control Strategy |
| **`Inp_HandFdbk`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] CV Feedback used in Hand Mode (CVEU) |
| **`Inp_HandFdbkBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inp_HandFdbk input quality or I/O Comms Status Bad, 0=OK |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Select Override control strategy |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Loop Mode Command 0=None, 1=Man, 2=Auto, 3=Casc, 4=Norm |
| **`Inp_OvrdRatio`** | `REAL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Loop Ratio in Override Mode (unitless) |
| **`Inp_OvrdSP`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Loop Auto Setpoint (SP) in Override Mode (PVEU) |
| **`Inp_OvrdCV`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Loop Manual Output (CV) in Override Mode (CVEU) |
| **`Inp_OvrdSPTarget`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Mode Setpoint Target for SP Ramping ('wizard') |
| **`Inp_OvrdSPRampT`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Setpoint Ramp Time for SP Ramping ('wizard') (sec). Valid = 0.0 to 28800.0 seconds. |
| **`Inp_OvrdSPRoCLimInc`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Setting for Setpoint Positive Rate of Change Limit (PVEU/sec) |
| **`Inp_OvrdSPRoCLimDec`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Setting for Setpoint Negative Rate of Change Limit (PVEU/sec) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_HiHiDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] High-High Deviation Status Gate, 1=enabled |
| **`Inp_HiDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] High Deviation Status Gate, 1=enabled |
| **`Inp_LoDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Low Deviation Status Gate, 1=enabled |
| **`Inp_LoLoDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Low-Low Deviation Status Gate, 1=enabled |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân đầu vào dùng để xóa (Reset) tất cả các cảnh báo đang chờ Reset từ chương trình PLC (1 = Xóa cảnh báo). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasRatio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Casc Loop Mode is Ratio, 0=Casc Loop Mode is Cascade |
| **`Cfg_HasCasc`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Enable the loop to be placed into cascade/ratio mode |
| **`Cfg_HasAuto`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Enable the loop to be placed into automatic mode |
| **`Cfg_HasMan`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Enable the loop to be placed into manual mode |
| **`Cfg_HasSPRamp`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Enable the Setpoint Ramp 'Wizard' function |
| **`Cfg_LockLM`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Lock Loop Mode: 0=not locked, 1=locked in Normal mode (see Cfg_NormLM) |
| **`Cfg_PVTrack`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=SP tracks PV in Manual, 0=no PV tracking |
| **`Cfg_CtrlAction`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Direct acting (E=PV-SP), 0=Reverse acting (E=SP-PV) |
| **`Cfg_Depend`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Dependent gains equation, 0=Independent gains |
| **`Cfg_DerivSmooth`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Use Derivative Smoothing |
| **`Cfg_ZCOff`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=need not cross zero error to be in deadband, 0=must cross zero error |
| **`Cfg_PropSPAct`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] When the bit is cleared (0, the default), the Proportional action is calculated based on the change in loop error, and a change in setpoint causes a change in loop error, resulting in a change in output. When the bit is set (1), the Proportional action is calculated based on the change in PV, and a change in setpoint results in no change in output. |
| **`Cfg_DerivSPAct`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] When the bit is cleared (0), the Derivative action is calculated based on the change in loop error, and a change in setpoint causes a change in loop error, resulting in a change in output. When the bit is set (1, the default), the Derivative action is calculated based on the change in PV, and a change in setpoint results in no change in output. |
| **`Cfg_LimitManCV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=CV limits enforced in Manual, 0=No CV Limiting in Manual |
| **`Cfg_InitToMan`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Go to Manual Loop Mode when an init request is seen |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to set up bumpless behavior of setting parameters when switching command sources. When this parameter is 1, in Program command source the operator settings track the program settings; in Operator command source the program settings track the operator settings; and the simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the operator settings and program settings are not modified by this instruction. In this case, when the command source is changed, the effective value of the setting can change depending on the program-set and operator-set values. |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Prog/Oper Settings track Override/Hand Inputs (CV, SP, Ratio) |
| **`Cfg_SkipSPRoCLim`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Skip SP RoC Limiting in Intlk, Maint, Ovrd modes |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI an interlock object (for example, P_Intlk) is used for Inp_IntlkOK and navigation to the interlock object’s faceplate is enabled IMPORTANT: The name of the Interlock object in the controller must be this object's name with the suffix ‘_Intlk’. For example, if your P_PIDE object has the name ’PIDE123’, then its Interlock object must be named ‘PIDE123_Intlk’. |
| **`Cfg_HasCascSPNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI to enable navigation to a connected cascade SP object |
| **`Cfg_HasPVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI to enable navigation to a connected PV object |
| **`Cfg_HasCVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI to enable navigation to a connected CV object |
| **`Cfg_OvrdIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Override ignores Bypassable Intlk; 0=always use Intlk |
| **`Cfg_IntlkTripLatch`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Latch in Interlock shed action until Reset 0=Return when Interlock clears |
| **`Cfg_SPFailLatch`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Latch in SP Fail shed action until Reset 0=Return when SP is good |
| **`Cfg_PVFailLatch`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Latch in PV Fail shed action until Reset 0=Return when PV is good |
| **`Cfg_CVFailLatch`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Latch in CV Fail shed action until Reset 0=Return when CV is good |
| **`Cfg_NormLM`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Normal Loop Mode 0=Disable Normal selection, 1=Man, 2=Auto, 3=Casc |
| **`Cfg_PwrupLM`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Powerup Loop Mode 0=No change (last), 1=Man (w/ CV), 2=Auto (w/ SP), 3=Casc, 4=Normal |
| **`Cfg_IntlkTripSPAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Interlock Trip Setpoint Action: 0=none, 1=hold last good, 2=use Cfg_IntlkSP, 3=set SP to curr. PV |
| **`Cfg_SPFailSPAct`** | `DINT` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] SP Fail Setpoint Action: 1=hold last good, 2=use Cfg_IntlkSP, 3=set SP to curr. PV |
| **`Cfg_PVFailSPAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] PV Fail Setpoint Action: 0=none, 1=hold last good, 2=use Cfg_IntlkSP |
| **`Cfg_CVFailSPAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Fail Setpoint Action: 0=none, 1=hold last good, 2=use Cfg_IntlkSP, 3=set SP to curr. PV |
| **`Cfg_IntlkTripCVAct`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Interlock Trip CV Action: 0=none, 1=hold last good, 2=use Cfg_IntlkCV |
| **`Cfg_SPFailCVAct`** | `DINT` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] SP Fail CV Action: 0=none, 1=hold last good, 2=use Cfg_IntlkCV |
| **`Cfg_PVFailCVAct`** | `DINT` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] PV Fail CV Action: 1=hold last good, 2=use Cfg_IntlkCV |
| **`Cfg_CVFailCVAct`** | `DINT` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] CV Fail CV Action: 1=hold last good, 2=use Cfg_IntlkCV |
| **`Cfg_IntlkTripLMAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Interlock Trip Loop Mode Action: 0=none, 1=Man only, 2=Auto or Man only |
| **`Cfg_SPFailLMAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] SP Fail Loop Mode Action: 0=none, 1=Man only, 2=Auto or Man only |
| **`Cfg_PVFailLMAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] PV Fail Loop Mode Action: 0=none, 1=Man only, 2=Auto or Man only |
| **`Cfg_CVFailLMAct`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Fail Loop Mode Action: 0=none, 1=Man only, 2=Auto or Man only |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị trên màn hình HMI of PV / SP (EU) |
| **`Cfg_CVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị trên màn hình HMI of CV (EU) |
| **`Cfg_HasHiHiDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasHiDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoLoDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HiHiDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoLoDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiHiDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoLoDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiHiDevSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_HiDevSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoDevSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoLoDevSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_MinRatio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum allowed Ratio (unitless) |
| **`Cfg_MaxRatio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum allowed Ratio (unitless) |
| **`Cfg_MinSP`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum allowed Setpoint (SP) value (PVEU) |
| **`Cfg_MaxSP`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum allowed Setpoint (SP) value (PVEU) |
| **`Cfg_MaxSPRoCInc`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum allowed Setpoint Positive Rate of Change Limit (PVEU/sec) |
| **`Cfg_MaxSPRoCDec`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum allowed Setpoint Negative Rate of Change Limit (PVEU/sec) |
| **`Cfg_SPRampMaxDev`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] If deviation exceeds this value, pause SP ramp (0.0=never pause) |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] PV Engineering Units Mimimum Value (PVEU) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] PV Engineering Units Maximum Value (PVEU) |
| **`Cfg_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Engineering Units Minimum Value (CVEU) |
| **`Cfg_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] CV engineering units maximum value (CV engineering units). TIP: The P_PIDE instruction supports reverse CV EU scaling, Cfg_CVEUMax can be less than (but not equal to) Cfg_CVEUMin. |
| **`Cfg_CVRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Output Units Minimum Value (CVRaw, for scaling) |
| **`Cfg_CVRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] CV Output Units Maximum Value (CVRaw, for scaling) |
| **`Cfg_ExecT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Execution Time (interval) for PID Algorithm (sec) |
| **`Cfg_PGain`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Proportional (independent) or Loop (dependent) Gain (unitless) |
| **`Cfg_IGain`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Integral Gain (1/min independent or min/repeat dependent) |
| **`Cfg_DGain`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Derivative Gain (min) |
| **`Cfg_ZCDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Normal Zero Crossing Deadband (PVEU) |
| **`Cfg_MinCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum allowed Controlled Variable (CV) value (CVEU) |
| **`Cfg_MaxCV`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum allowed Controlled Variable (CV) value (CVEU) |
| **`Cfg_CVRoCLim`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Rate of Change Limit (CVEU/sec) |
| **`Cfg_MaxInactiveCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] When Val_CV is greater than this value (in CV EU) set Sts_Active (for HMI) |
| **`Cfg_IntlkSP`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] SP Value to use with Interlock / Bad Value SP action (PVEU) |
| **`Cfg_IntlkCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Value to use with Interlock / Bad Value CV action (CVEU) |
| **`Cfg_PwrupSP`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Loop SP on powerup, used if Cfg_PwrupLM = 1 (Manual) or 2 (Auto) (PVEU) |
| **`Cfg_PwrupCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Loop CV on powerup, used if Cfg_PwrupLM <> 0 (none) (CVEU) |
| **`Cfg_HiHiDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the minimum time (in seconds) the loop deviation must remain beyond the status threshold for the status to be set. On-delay times are used to avoid unnecessary alarms when the deviation momentarily overshoots its threshold (for example, Val_HiHiDevLim). |
| **`Cfg_HiDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the minimum time (in seconds) the loop deviation must remain beyond the status threshold for the status to be set. On-delay times are used to avoid unnecessary alarms when the deviation momentarily overshoots its threshold (for example, Val_HiHiDevLim). |
| **`Cfg_LoDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the minimum time (in seconds) the loop deviation must remain beyond the status threshold for the status to be set. On-delay times are used to avoid unnecessary alarms when the deviation momentarily overshoots its threshold (for example, Val_HiHiDevLim). |
| **`Cfg_LoLoDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the minimum time (in seconds) the loop deviation must remain beyond the status threshold for the status to be set. On-delay times are used to avoid unnecessary alarms when the deviation momentarily overshoots its threshold (for example, Val_HiHiDevLim). |
| **`Cfg_HiHiDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the amount of time (in seconds) the loop deviation must stay within each status threshold to clear the status. Off delay times are used to reduce chattering alarms. EXAMPLE: If Cfg_HiDevOffDly is 5 seconds, the loop deviation must be below the status limit (Val_HiHiDevLim) minus deadband (Cfg_DevDB) for 5 seconds before the status is returned to normal. |
| **`Cfg_HiDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the amount of time (in seconds) the loop deviation must stay within each status threshold to clear the status. Off delay times are used to reduce chattering alarms. EXAMPLE: If Cfg_HiDevOffDly is 5 seconds, the loop deviation must be below the status limit (Val_HiHiDevLim) minus deadband (Cfg_DevDB) for 5 seconds before the status is returned to normal. |
| **`Cfg_LoDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the amount of time (in seconds) the loop deviation must stay within each status threshold to clear the status. Off delay times are used to reduce chattering alarms. EXAMPLE: If Cfg_HiDevOffDly is 5 seconds, the loop deviation must be below the status limit (Val_HiHiDevLim) minus deadband (Cfg_DevDB) for 5 seconds before the status is returned to normal. |
| **`Cfg_LoLoDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the amount of time (in seconds) the loop deviation must stay within each status threshold to clear the status. Off delay times are used to reduce chattering alarms. EXAMPLE: If Cfg_HiDevOffDly is 5 seconds, the loop deviation must be below the status limit (Val_HiHiDevLim) minus deadband (Cfg_DevDB) for 5 seconds before the status is returned to normal. |
| **`Cfg_HiHiDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_HiDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_LoDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_LoLoDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_HiHiDevLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Current High-High Deviation Status Threshold (PVEU) |
| **`Cfg_HiHiDevDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] High-High Devation Alarm Deadband (PVEU) |
| **`Cfg_HiDevLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Current High Deviation Status Threshold (PVEU) |
| **`Cfg_HiDevDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] High Devation Alarm Deadband (PVEU) |
| **`Cfg_LoDevLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Current Low Deviation Status Threshold (PVEU) |
| **`Cfg_LoDevDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Low Devation Alarm Deadband (PVEU) |
| **`Cfg_LoLoDevLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Current Low-Low Deviation Status Threshold (PVEU) |
| **`Cfg_LoLoDevDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Low-Low Devation Alarm Deadband (PVEU) |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Oper keeps control: .0=Loop Mode .1=CV .2=SP, .3=Ratio |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Prog keeps control: .0=Loop Mode .1=CV .2=SP, .3=Ratio |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Ext keeps control: .0=Loop Mode .1=CV .2=SP, .3=Ratio |
| **`PSet_Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting for Ratio (loop CasRat and Ratio enabled) (unitless) |
| **`PSet_SP`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting for Setpoint (loop Auto) (PVEU) |
| **`PSet_SPRoCLimInc`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting for Setpoint Positive Rate of Change Limit Increasing (PVEU/sec) |
| **`PSet_SPRoCLimDec`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting for Setpoint Negative Rate of Change Limit Decreasing (PVEU/sec) |
| **`PSet_SPTarget`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting for Setpoint Ramp Target (endpoint, for ramp wizard) (PVEU) |
| **`PSet_SPRampT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting for Setpoint Ramp Time (to reach target, for ramp wizard) (sec). Valid = 0.0 to 28800.0 seconds. |
| **`PSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting for Controlled Variable (loop Manual) (CVEU) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`XSet_Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting for Ratio (loop CasRat and Ratio enabled) (unitless) |
| **`XSet_SP`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting for Setpoint (loop Auto) (PVEU) |
| **`XSet_SPRoCLimInc`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting for Setpoint Positive Rate of Change Limit Increasing (PVEU/sec) |
| **`XSet_SPRoCLimDec`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting for Setpoint Negative Rate of Change Limit Decreasing (PVEU/sec) |
| **`XSet_SPTarget`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting for Setpoint Ramp Target (endpoint, for ramp wizard) (PVEU) |
| **`XSet_SPRampT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting for Setpoint Ramp Time (to reach target, for ramp wizard) (sec). Valid = 0.0 to 28800.0 seconds. |
| **`XSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting for Controlled Variable (loop Manual) (CVEU) |
| **`PCmd_Casc`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Cascade / Ratio Loop Mode |
| **`PCmd_Auto`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Automatic Loop Mode |
| **`PCmd_Man`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Manual Loop Mode |
| **`PCmd_NormLM`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Người vận hành (Operator HMI) để select Normal Loop Mode (see Cfg_NormLM) |
| **`PCmd_SPRampStart`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để initiate SP ramping |
| **`PCmd_SPRampStop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để stop SP ramping |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiHiDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoLoDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiHiDevSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) High-High Deviation Alarm |
| **`PCmd_HiDevSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) High Deviation Alarm |
| **`PCmd_LoDevSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Low Deviation Alarm |
| **`PCmd_LoLoDevSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Low-Low Deviation Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Loop Failure Alarm |
| **`PCmd_HiHiDevUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) High-High Deviation Alarm |
| **`PCmd_HiDevUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) High Deviation Alarm |
| **`PCmd_LoDevUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Low Deviation Alarm |
| **`PCmd_LoLoDevUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Low-Low Deviation Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Loop Failure Alarm |
| **`PCmd_HiHiDevUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiDevUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoDevUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoLoDevUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |
| **`XCmd_Casc`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để select Cascade / Ratio control |
| **`XCmd_Auto`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để select Automatic control |
| **`XCmd_Man`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để select Manual control |
| **`XCmd_NormLM`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để select Normal Loop Mode (see Cfg_NormLM) |
| **`XCmd_SPRampStart`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để initiate SP ramping |
| **`XCmd_SPRampStop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để stop SP ramping |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Reset all Alarms requiring Reset |

## 3. Bảng chi tiết 149 Biến Đầu Ra (`Output Parameters`) của `P_PIDE`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_CV`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] CV to final control element (CV Raw Units) |
| **`Val_PV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of Loop PV (PVEU) |
| **`Val_Ratio`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of Loop Ratio (unitless) |
| **`Val_SPSet`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of selected SP (before clamping and ramping) (PVEU) |
| **`Val_SP`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of SP being used (after ramping and clamping (PVEU) |
| **`Val_SPTarget`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Accepted Setting for Setpoint Ramp Target (endpoint, for ramp wizard) (PVEU) |
| **`Val_SPRampT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Accepted Setting for Setpoint Ramp Time (to reach target, for ramp wizard) (sec). Valid = 0.0 to 28800.0 seconds. |
| **`Val_SPRampRoC`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Calculated Value of SP Rate of Change for Ramping (from Ramp Wizard) (PVEU/sec) |
| **`Val_SPRoCInc`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Value of Setpoint Positive Rate of Change Limit Increasing (PVEU/sec) |
| **`Val_SPRoCDec`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Value of Setpoint Negative Rate of Change Limit Decreasing (PVEU/sec) |
| **`Val_E`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Loop Error (PV-SP) (PVEU) |
| **`Val_CVSet`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of selected CV (before clamping and ramping) (CVEU) |
| **`Val_CVOut`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of CV Output (after ramping and clamping (CVEU) |
| **`Val_PVPercent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Loop PV (percent of span) |
| **`Val_SPPercent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Loop SP (percent of span) |
| **`Val_EPercent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Loop Error (PV-SP) (percent of span) |
| **`Val_CVPercent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Loop CV (percent of span) |
| **`Val_ActExecT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Actual PID Algorithm Execution Time (Interval) (sec) |
| **`Val_Init`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Initialization Value for this loop's Cascade Primary (PVEU) |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of PV scaled range (PVEU) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of PV scaled range (PVEU) |
| **`Val_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of CV scaled range (CVEU) |
| **`Val_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of CV scaled range (CVEU) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Loop status enumeration: 0 = Unknown (POoS @ Intlk CV) 1 = Manual Loop Mode 2 = Auto Loop Mode 3 = Cascade Loop Mode (no ratio) 4 = Cascade Loop Mode with Ratio |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Loop fault status: 0 = No fault 1 = PV uncertain 22 = Low deviation 23 = High deviation 27 = Low-low deviation 28 = High-high deviation 29 = Shed on interlock trip 30 = Shed on SP failure 31 = Shed on PV failure 32 = Shed on CV failure 34 = Configuration error |
| **`Val_State`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Loop algorithm state: 0 = Manual 1 = Controlled, within deadband 2 = Controlled, not within deadband 3 = SP is ramping 4 = CV is ramping 5 = Ratio is clamped 6 = SP is clamped 7 = CV is clamped 8 = Windup low 9 = Windup high 10 = SP held (shed) 11 = SP at Interlock SP (shed) 12 = CV held (shed) 13 = CV at Interlock CV (shed) 14 = CV at Hand Feedback value 15 = CV initializing |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Highest Alarm prio and ack status this object + PV, CV, CascSP (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_InstrFaults1`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PIDE Block Status1 Instruction Fault bits (See PIDE Instruction Help) |
| **`Sts_InstrFaults2`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PIDE Block Status2 Instruction Fault bits (See PIDE Instruction Help) |
| **`Sts_Casc`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Loop Mode is Cascade/Ratio |
| **`Sts_Auto`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Loop Mode is Auto |
| **`Sts_Man`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Loop Mode is Manual |
| **`Sts_NormLM`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Loop is in Normal Loop Mode (see Cfg_NormLM) |
| **`Sts_Initializing`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV is initializing because of Request from this loop's Secondary |
| **`Sts_InitReq`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV Initialize Request to this loop's Primary |
| **`Sts_WindupHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=This loop winding up High, to Inp_WindupHi of this loop's Primary |
| **`Sts_WindupLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=This loop winding up Low, to Inp_WindupLo of this loop's Primary |
| **`Sts_ZCDBOn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Deadband indicator: 1=PV is within ZC deadband and CV will not change |
| **`Sts_RatioClamped`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Selected Ratio (PSet/OSet/XSet_Ratio or Inp_OvrdRatio) has been clamped |
| **`Sts_SPBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=SP Value is Bad (quality) or invalid or Casc SP comms fault |
| **`Sts_IntlkSP`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=SP Value is being set by shed to Interlock SP |
| **`Sts_SPHeld`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=SP Value is being set by shed to hold last good SP |
| **`Sts_SPShedPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=SP Value was set by shed to current PV |
| **`Sts_SPShed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=SP from Shed, 0=SP from Prog, Oper, Ovrd (A), Casc SP Input (C) or PV Track (M) |
| **`Sts_SPTrackPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=SP Value is being set by PV Tracking (in Manual Mode) |
| **`Sts_SPClamped`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Selected SP is being clamped (for faceplate animation) |
| **`Sts_SPRamping`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=SP is ramping toward Val_SPSet, 0=ramp complete |
| **`Sts_SkipSPRoCLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=SP rate limiting is being skipped (for faceplate animation) |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=PV Value, comms, quality or EU Limitis Bad |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV có chất lượng không chắc chắn (Uncertain Quality) |
| **`Sts_FFBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Feedforward Term Value is invalid |
| **`Sts_FFPrevBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_FFPrev is <-100 CV%, >100 CV%, or not a number (NaN) |
| **`Sts_CVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV Value is Bad (quality) or invalid or CV comms fault |
| **`Sts_CVPrevBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CVPrev is <0.0 CV%, >100.0 CV%, or not a number (NaN) |
| **`Sts_HandFdbkBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Hand FB (Tieback) Value is Bad (quality) or invalid or comms fault |
| **`Sts_IntlkCV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV ivalue s being set by shed to Interlock CV |
| **`Sts_CVHeld`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV Value is being set by shed to hold last good CV |
| **`Sts_CVShed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV from Shed, 0=CV from Prog, Oper, Ovrd (Man), or PID (Auto, Casc) |
| **`Sts_CVClamped`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Selected CV is being clamped (for faceplate animation) |
| **`Sts_CVRamping`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV is ramping toward Val_CVSet, 0=ramp complete |
| **`Sts_Active`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV is greater than Cfg_MaxInactiveCV, show graphic symbol as "active" |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Analog Output available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Init`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Initialized to Manual Mode |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Out of Service |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device has a Maintenance Bypass function active |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail Err bits for reason |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Status On-Delay or Off-Delay Time Invalid |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Alarm Severity Invalid |
| **`Err_RatioLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Ratio Clamping Limits Invalid |
| **`Err_SPLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Setpoint Clamping Limits Invalid |
| **`Err_CVLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV Clamping Limits Invalid |
| **`Err_PVEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: PV EU Max/Min Invalid |
| **`Err_CVEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV EU Max/Min Invalid |
| **`Err_CVRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV Raw Max/Min Invalid |
| **`Err_ZCDB`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Loop Zero Crossing Deadband Invalid |
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
| **`Sts_HiHiDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] High-High Deviation kết quả so sánh ngưỡng trực tiếp 1=High-High |
| **`Sts_HiHiDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] High-High Deviation trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiHiDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=PV Deviation đang vượt trên ngưỡng Rất Cao (High-High) |
| **`Alm_HiHiDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Loop High-High Deviation Alarm |
| **`Ack_HiHiDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=High-High Deviation Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_HiHiDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High-High Deviation Alarm is Disabled (by Maint) |
| **`Sts_HiHiDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High-High Deviation Alarm is Shelved (by Oper) |
| **`Sts_HiHiDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High-High Deviation Alarm is Suppressed (by logic) |
| **`Sts_HiDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] High Deviation kết quả so sánh ngưỡng trực tiếp 1=High-High |
| **`Sts_HiDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] High Deviation trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=PV Deviation đang vượt trên ngưỡng Cao (High) |
| **`Alm_HiDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Loop High Deviation Alarm |
| **`Ack_HiDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=High Deviation Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_HiDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Deviation Alarm is Disabled (by Maint) |
| **`Sts_HiDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Deviation Alarm is Shelved (by Oper) |
| **`Sts_HiDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Deviation Alarm is Suppressed (by logic) |
| **`Sts_LoDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Low Deviation kết quả so sánh ngưỡng trực tiếp 1=High-High |
| **`Sts_LoDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Low Deviation trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_LoDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=PV Deviation đang tụt dưới ngưỡng Thấp (Low) |
| **`Alm_LoDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Loop Low Deviation Alarm |
| **`Ack_LoDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Loiw Deviation Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_LoDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Deviation Alarm is Disabled (by Maint) |
| **`Sts_LoDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Deviation Alarm is Shelved (by Oper) |
| **`Sts_LoDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Deviation Alarm is Suppressed (by logic) |
| **`Sts_LoLoDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Low-Low Deviation kết quả so sánh ngưỡng trực tiếp 1=High-High |
| **`Sts_LoLoDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Low-Low Deviation trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_LoLoDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=PV Deviation đang tụt dưới ngưỡng Rất Thấp (Low-Low) |
| **`Alm_LoLoDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Loop Low-Low Deviation Alarm |
| **`Ack_LoLoDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Low-Loiw Deviation Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_LoLoDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low-Low Deviation Alarm is Disabled (by Maint) |
| **`Sts_LoLoDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low-Low Deviation Alarm is Shelved (by Oper) |
| **`Sts_LoLoDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low-Low Deviation Alarm is Suppressed (by logic) |
| **`Sts_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock NOT OK caused loop output to hold or change |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Interlock Trip Alarm |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm is Disabled (by Maint) |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm is Shelved (by Oper) |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm is Suppressed (by logic) |
| **`Sts_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Loop Failure: PV Bad, SP Bad or CV Comm Failure or Bad |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Loop Failure Alarm: PV Bad, SP Bad or CV Comm Failure or Bad |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Loop Failure Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Loop Failure Alarm is Disabled (by Maint) |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Loop Failure Alarm is Shelved (by Oper) |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Loop Failure Alarm is Suppressed (by logic) |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_PIDE`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 59 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PIDE`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_CascSPNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tagname for destination of Cascade SP Navigation button |
| **`Cfg_CVEU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] CV Engineering Units displayed on HMI |
| **`Cfg_CVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tagname for destination of CV Navigation button |
| **`Cfg_Desc`** | `STRING_40` | `'Advanced PID Control Loop'` | [Biến cấu hình thông số] Description displayed on HMI |
| **`Cfg_Label`** | `STRING_20` | `'PID Loop'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_PVEU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] PV / Setpoint Engineering Units displayed on HMI |
| **`Cfg_PVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tagname for destination of PV Navigation button |
| **`Cfg_Tag`** | `STRING_20` | `'P_PIDAdv'` | [Biến cấu hình thông số] Tagname displayed on HMI |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`Fail`** | `P_Alarm` | `{...}` | Loop (SP, PV or CV) Failure Alarm |
| **`HiDev`** | `P_Alarm` | `{...}` | High Deviation Alarm |
| **`HiDevGate`** | `P_Gate` | `{...}` | High Deviation Gate/Delay |
| **`HiHiDev`** | `P_Alarm` | `{...}` | High-High Deviation Alarm |
| **`HiHiDevGate`** | `P_Gate` | `{...}` | High-High Deviation Gate/Delay |
| **`HMI_HasAutotune`** | `BOOL` | `0` | [Biến nội bộ giao diện HMI Faceplate] 1=P_PIDE_Only AOI has been modified to include Autotune par. on PIDE block |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_PIDE'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`LoDev`** | `P_Alarm` | `{...}` | Low Deviation Alarm |
| **`LoDevGate`** | `P_Gate` | `{...}` | Low Deviation Gate/Delay |
| **`LoLoDev`** | `P_Alarm` | `{...}` | Low-Low Deviation Alarm |
| **`LoLoDevGate`** | `P_Gate` | `{...}` | Low-Low Deviation Gate/Delay |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Auto`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để select Automatic control |
| **`OCmd_Casc`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để select Cascade / Ratio control |
| **`OCmd_Man`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để select Manual control |
| **`OCmd_NormLM`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để select Normal Loop Mode (see Cfg_NormLM) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions |
| **`OCmd_SPRampStart`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để initiate SP ramping |
| **`OCmd_SPRampStop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để stop SP ramping |
| **`ORdy_Auto`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Auto (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Casc`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Casc (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_CV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_CV (enables data entry field) |
| **`ORdy_Man`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Man (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_NormLM`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_NormLM (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Ratio`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_Ratio (enables data entry field) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=At least one Alarm or latched Shed condition requires Reset or Ack |
| **`ORdy_SP`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_SP (enables data entry field) |
| **`ORdy_SPRampStart`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_RampSP (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SPRampStop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_RampStop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SPRampT`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_SPRampT (enables data entry field) |
| **`ORdy_SPRoCLimDec`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_SPRoCLimDec (enables data entry field) |
| **`ORdy_SPRoCLimInc`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_SPRoCLimInc (enables data entry field) |
| **`ORdy_SPTarget`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_SPTarget (enables data entry field) |
| **`OSet_CV`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting for Controlled Variable (loop Manual) (CVEU) |
| **`OSet_Ratio`** | `REAL` | `1` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting for Ratio (loop CasRat and Ratio enabled) (unitless) |
| **`OSet_SP`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting for Setpoint (loop Auto) (PVEU) |
| **`OSet_SPRampT`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting for Setpoint Ramp Time (to reach target, for ramp wizard) (sec). Valid = 0.0 to 28800.0 seconds. |
| **`OSet_SPRoCLimDec`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting for Setpoint Negative Rate of Change Limit (PVEU/sec) |
| **`OSet_SPRoCLimInc`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting for Setpoint Positive Rate of Change Limit (PVEU/sec) |
| **`OSet_SPTarget`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting for Setpoint Ramp Target (endpoint, for ramp wizard) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Fanout`
**Thuộc chương:** Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Fanout`:** **77** Biến đầu vào (`Input Parameters`) | **61** Biến đầu ra (`Output Parameters`) | **26** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Fanout`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_CV`, `Inp_CV1InitVal`, `Inp_CV2InitVal`, `Inp_CV3InitVal`, `Inp_CV4InitVal`, `Inp_CV5InitVal`, `Inp_CV6InitVal`, `Inp_CV7InitVal`, `Inp_CV8InitVal`, `Inp_CV1InitReq`, `Inp_CV2InitReq`, `Inp_CV3InitReq`, `Inp_CV4InitReq`, `Inp_CV5InitReq`, `Inp_CV6InitReq`, `Inp_CV7InitReq`, `Inp_CV8InitReq`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasCV2`, `Cfg_HasCV3`, `Cfg_HasCV4`, `Cfg_HasCV5`, `Cfg_HasCV6`, `Cfg_HasCV7`, `Cfg_HasCV8`, `Cfg_HasMoreObj`, `Cfg_FixedInitVal`, `Cfg_UseFixedInit`, `Cfg_ShedHold`, `Cfg_HasCVNav`, `Cfg_HasNav`, `Cfg_CVEUMin`, `Cfg_CVEUMax`, `Cfg_CVMin`, `Cfg_CVMax`, `Cfg_CVRoCLim`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_CV_EU`, `Cfg_CV1_EU`, `Cfg_CV1_Label`, `Cfg_CV2_EU`, `Cfg_CV2_Label`, `Cfg_CV3_EU`, `Cfg_CV3_Label`, `Cfg_CV4_EU`, `Cfg_CV4_Label`, `Cfg_CV5_EU`, `Cfg_CV5_Label`, `Cfg_CV6_EU`, `Cfg_CV6_Label`, `Cfg_CV7_EU`, `Cfg_CV7_Label`, `Cfg_CV8_EU`, `Cfg_CV8_Label`, `Cfg_CVNavTag`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Fanout` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_CV1`, `Out_CV2`, `Out_CV3`, `Out_CV4`, `Out_CV5`, `Out_CV6`, `Out_CV7`, `Out_CV8`, `Out_CVInitVal`, `Out_CVInitReq`, `Val_CVEUMin`, `Val_CVEUMax`, `Val_InpCV`, `Val_CV`, `Val_MinCVIn1`, `Val_MinCVIn2`, `Val_MinCVIn3`, `Val_MinCVIn4`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 77 Biến Đầu Vào (`Input Parameters`) của `P_Fanout`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_CV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input CV from Upstream block's output (EU) |
| **`Inp_CV1InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV2InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV3InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV4InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV5InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV6InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV7InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV8InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV1InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV2InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV3InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV4InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV5InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV6InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV7InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV8InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Cfg_HasCV2`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasCV3`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasCV4`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasCV5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasCV6`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasCV7`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasCV8`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_FixedInitVal`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Fixed Initialization value (in Inp_CV eng. units), used if Cfg_UseFixedInit=1 |
| **`Cfg_UseFixedInit`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Use Cfg_FixedInitVal to initialize primary, 0=Use Inp_CV1InitVal |
| **`Cfg_ShedHold`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Hold Output on Inf / NaN Input, 0=Copy Inf / NaN through |
| **`Cfg_HasCVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI to enable navigation to a connected input CV object |
| **`Cfg_HasNav`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set bits indicate which Navigation buttons are enabled .0=CV1, .1=CV2, ... .7=CV8 |
| **`Cfg_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to the range of the signal connected to Inp_CV. |
| **`Cfg_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to the range of the signal connected to Inp_CV. |
| **`Cfg_CVMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Input CV minimum (Lo Clamp) or CV maximum (Hi Clamp) (in engineering units). |
| **`Cfg_CVMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Input CV minimum (Lo Clamp) or CV maximum (Hi Clamp) (in engineering units). |
| **`Cfg_CVRoCLim`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Program Setting for Input CV Rate of Change Limit (inc or dec) (Inp EU/sec) |
| **`Cfg_CV1Ratio`** | `REAL` | `-2` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV2Ratio`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV3Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV4Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV5Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV6Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV7Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV8Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV1Offset`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV2Offset`** | `REAL` | `-100` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV3Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV4Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV5Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV6Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV7Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV8Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV1Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV2Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV3Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV4Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV5Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV6Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV7Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV8Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV1Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV2Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV3Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV4Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV5Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV6Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV7Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV8Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV1TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV2TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV3TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV4TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV5TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV6TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV7TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV8TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CVDecPlcs`** | `SINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for CV display (0..6) |

## 3. Bảng chi tiết 61 Biến Đầu Ra (`Output Parameters`) của `P_Fanout`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Output - System Defined Parameter |
| **`Out_CV1`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV2`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV3`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV4`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV5`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV6`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV7`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV8`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CVInitVal`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Initialization Value to Upstream block (Inp_CV EU) |
| **`Out_CVInitReq`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Iniitialization Request to Upstream block (1=Initialize) |
| **`Val_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of scaled range = MIN (Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of scaled range = MAX (Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_InpCV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of Inp_CV (not clamped or ramped) (EU) |
| **`Val_CV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of CV after clamping and ramping (EU) |
| **`Val_MinCVIn1`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn2`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn3`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn4`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn5`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn6`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn7`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn8`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn1`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn2`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn3`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn4`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn5`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn6`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn7`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn8`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Sts_CVInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV is Infinite or Not A Number (1.$, 1.#NaN) |
| **`Sts_CVLimited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Val_CV (selected CV) clamped at configured Max/Min |
| **`Sts_CV1InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV1InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV1Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV1 clamped at configured Max/Min |
| **`Sts_CV2InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV2InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV2Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV2 clamped at configured Max/Min |
| **`Sts_CV3InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV3InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV3Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV3 clamped at configured Max/Min |
| **`Sts_CV4InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV4InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV4Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV4 clamped at configured Max/Min |
| **`Sts_CV5InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV5InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV5Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV5 clamped at configured Max/Min |
| **`Sts_CV6InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV6InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV6Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV6 clamped at configured Max/Min |
| **`Sts_CV7InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV7InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV7Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV7 clamped at configured Max/Min |
| **`Sts_CV8InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV8InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV8Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV8 clamped at configured Max/Min |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV Clamp Limits crossed (Max<Min) |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV Scale EU Min = Max |
| **`Err_CV1Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV1 Clamp Limits crossed (Max<Min) |
| **`Err_CV2Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV2 Clamp Limits crossed (Max<Min) |
| **`Err_CV3Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV3 Clamp Limits crossed (Max<Min) |
| **`Err_CV4Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV4 Clamp Limits crossed (Max<Min) |
| **`Err_CV5Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV5 Clamp Limits crossed (Max<Min) |
| **`Err_CV6Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV6 Clamp Limits crossed (Max<Min) |
| **`Err_CV7Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV7 Clamp Limits crossed (Max<Min) |
| **`Err_CV8Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV8 Clamp Limits crossed (Max<Min) |
| **`P_Fanout`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 26 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Fanout`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_CV_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for display on HMI |
| **`Cfg_CV1_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 1 Engineering Units for display on HMI |
| **`Cfg_CV1_Label`** | `STRING_20` | `'Output 1'` | [Biến cấu hình thông số] Output 1 Label for display on HMI |
| **`Cfg_CV2_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 2 Engineering Units for display on HMI |
| **`Cfg_CV2_Label`** | `STRING_20` | `'Output 2'` | [Biến cấu hình thông số] Output 2 Label for display on HMI |
| **`Cfg_CV3_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 3 Engineering Units for display on HMI |
| **`Cfg_CV3_Label`** | `STRING_20` | `'Output 3'` | [Biến cấu hình thông số] Output 3 Label for display on HMI |
| **`Cfg_CV4_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 4 Engineering Units for display on HMI |
| **`Cfg_CV4_Label`** | `STRING_20` | `'Output 4'` | [Biến cấu hình thông số] Output 4 Label for display on HMI |
| **`Cfg_CV5_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 5 Engineering Units for display on HMI |
| **`Cfg_CV5_Label`** | `STRING_20` | `'Output 5'` | [Biến cấu hình thông số] Output 5 Label for display on HMI |
| **`Cfg_CV6_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 6 Engineering Units for display on HMI |
| **`Cfg_CV6_Label`** | `STRING_20` | `'Output 6'` | [Biến cấu hình thông số] Output 6 Label for display on HMI |
| **`Cfg_CV7_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 7 Engineering Units for display on HMI |
| **`Cfg_CV7_Label`** | `STRING_20` | `'Output 7'` | [Biến cấu hình thông số] Output 7 Label for display on HMI |
| **`Cfg_CV8_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 8 Engineering Units for display on HMI |
| **`Cfg_CV8_Label`** | `STRING_20` | `'Output 8'` | [Biến cấu hình thông số] Output 8 Label for display on HMI |
| **`Cfg_CVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tagname for destination of input CV Navigation button |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Fanout'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Fanout'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_NavTag`** | `STRING_NavTag[8]` | `{...}` | [Biến cấu hình thông số] Tagnames for destinations of Navigation buttons ([0]=output 1, ... [7]=output 8) |
| **`Cfg_Tag`** | `STRING_20` | `'P_Fanout'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Fanout'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_HiLoSel`
**Thuộc chương:** Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_HiLoSel`:** **46** Biến đầu vào (`Input Parameters`) | **21** Biến đầu ra (`Output Parameters`) | **17** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_HiLoSel`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_1`, `Inp_E1`, `Inp_Kp1`, `Inp_2`, `Inp_E2`, `Inp_Kp2`, `Inp_3`, `Inp_E3`, `Inp_Kp3`, `Inp_4`, `Inp_E4`, `Inp_Kp4`, `Inp_5`, `Inp_E5`, `Inp_Kp5`, `Inp_6`, `Inp_E6`, `Inp_Kp6`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HiLoSel`, `Cfg_HasInp1`, `Cfg_UseInp1`, `Cfg_Inp1Offset`, `Cfg_HasInp2`, `Cfg_UseInp2`, `Cfg_Inp2Offset`, `Cfg_HasInp3`, `Cfg_UseInp3`, `Cfg_Inp3Offset`, `Cfg_HasInp4`, `Cfg_UseInp4`, `Cfg_Inp4Offset`, `Cfg_HasInp5`, `Cfg_UseInp5`, `Cfg_Inp5Offset`, `Cfg_HasInp6`, `Cfg_UseInp6`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Inp1_Label`, `Cfg_Inp2_Label`, `Cfg_Inp3_Label`, `Cfg_Inp4_Label`, `Cfg_Inp5_Label`, `Cfg_Inp6_Label`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_OutNavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_HiLoSel` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out`, `Out_Inp1Prev`, `Out_UseInp1Prev`, `Out_Inp2Prev`, `Out_UseInp2Prev`, `Out_Inp3Prev`, `Out_UseInp3Prev`, `Out_Inp4Prev`, `Out_UseInp4Prev`, `Out_Inp5Prev`, `Out_UseInp5Prev`, `Out_Inp6Prev`, `Out_UseInp6Prev`, `Val_Out`, `Val_Selected`, `Sts_MaintByp`, `Sts_Err`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 46 Biến Đầu Vào (`Input Parameters`) của `P_HiLoSel`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input #1 |
| **`Inp_E1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Loop Error from Primary #1 (opt., for offset calc) |
| **`Inp_Kp1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Prop. Gain from Primary #1 (opt. for offset calc) |
| **`Inp_2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input #2 |
| **`Inp_E2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Loop Error from Primary #2 (opt., for offset calc) |
| **`Inp_Kp2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Prop. Gain from Primary #2 (opt. for offset calc) |
| **`Inp_3`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input #3 |
| **`Inp_E3`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Loop Error from Primary #3 (opt., for offset calc) |
| **`Inp_Kp3`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Prop. Gain from Primary #3 (opt. for offset calc) |
| **`Inp_4`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input #4 |
| **`Inp_E4`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Loop Error from Primary #4 (opt., for offset calc) |
| **`Inp_Kp4`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Prop. Gain from Primary #4 (opt. for offset calc) |
| **`Inp_5`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input #5 |
| **`Inp_E5`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Loop Error from Primary #5 (opt., for offset calc) |
| **`Inp_Kp5`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Prop. Gain from Primary #5 (opt. for offset calc) |
| **`Inp_6`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input #6 |
| **`Inp_E6`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Loop Error from Primary #6 (opt., for offset calc) |
| **`Inp_Kp6`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Prop. Gain from Primary #6 (opt. for offset calc) |
| **`Inp_InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization Value from downstream block |
| **`Inp_InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization Value from downstream block (1 = init to Inp_InitVal) |
| **`Cfg_HiLoSel`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Selection: 1=High-Select, 0=Low-Select |
| **`Cfg_HasInp1`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_1 is connected |
| **`Cfg_UseInp1`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_1 is included in selection (for Maintenance use) |
| **`Cfg_Inp1Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use Inp1Init = Out +/- Kp1*E1 Offset, 0 = no Offset |
| **`Cfg_HasInp2`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_2 is connected |
| **`Cfg_UseInp2`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_2 is included in selection (for Maintenance use) |
| **`Cfg_Inp2Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use Inp2Init = Out +/- Kp2*E2 Offset, 0 = no Offset |
| **`Cfg_HasInp3`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_3 is connected |
| **`Cfg_UseInp3`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_3 is included in selection (for Maintenance use) |
| **`Cfg_Inp3Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use Inp3Init = Out +/- Kp3*E3 Offset, 0 = no Offset |
| **`Cfg_HasInp4`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_4 is connected |
| **`Cfg_UseInp4`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_4 is included in selection (for Maintenance use) |
| **`Cfg_Inp4Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use Inp4Init = Out +/- Kp4*E4 Offset, 0 = no Offset |
| **`Cfg_HasInp5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_5 is connected |
| **`Cfg_UseInp5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_5 is included in selection (for Maintenance use) |
| **`Cfg_Inp5Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use Inp5Init = Out +/- Kp5*E5 Offset, 0 = no Offset |
| **`Cfg_HasInp6`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_6 is connected |
| **`Cfg_UseInp6`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_6 is included in selection (for Maintenance use) |
| **`Cfg_Inp6Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use Inp6Init = Out +/- Kp6*E6 Offset, 0 = no Offset |
| **`Cfg_DecPlcs`** | `SINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị trên màn hình HMI (0..6) |
| **`Cfg_HasOutNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI to enable navigation to a connected output Output object |
| **`Cfg_HasNav`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set bits indicate which Navigation buttons are enabled .0=Inp1, ..., .5=Inp6 |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_OutMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum allowed output (clamp) |
| **`Cfg_OutMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum allowed output (clamp) |

## 3. Bảng chi tiết 21 Biến Đầu Ra (`Output Parameters`) của `P_HiLoSel`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Output - System Defined Parameter |
| **`Out`** | `REAL` | `0` | Hiện (Pin) | Output (selected min or max) for downstream block |
| **`Out_Inp1Prev`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Previous (Feedback) Input Value for Primary #1 |
| **`Out_UseInp1Prev`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Request for Primary #1 to use feedback Out_Inp1Prev |
| **`Out_Inp2Prev`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Previous (Feedback) Input Value for Primary #2 |
| **`Out_UseInp2Prev`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Request for Primary #2 to use feedback Out_Inp2Prev |
| **`Out_Inp3Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Previous (Feedback) Input Value for Primary #3 |
| **`Out_UseInp3Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Request for Primary #3 to use feedback Out_Inp3Prev |
| **`Out_Inp4Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Previous (Feedback) Input Value for Primary #4 |
| **`Out_UseInp4Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Request for Primary #4 to use feedback Out_Inp4Prev |
| **`Out_Inp5Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Previous (Feedback) Input Value for Primary #5 |
| **`Out_UseInp5Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Request for Primary #5 to use feedback Out_Inp5Prev |
| **`Out_Inp6Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Previous (Feedback) Input Value for Primary #6 |
| **`Out_UseInp6Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Request for Primary #6 to use feedback Out_Inp6Prev |
| **`Val_Out`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Output (selected min or max Input) for HMI |
| **`Val_Selected`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Selected Inp 0=Min, 1=Inp1, 2=Inp2, 3=Inp3, 4=Inp4, 5=Inp5, 6=Inp6, 7=Max, 8=Inp_InitVal |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Error in Configuration, check Err_ bits for reason |
| **`Err_Has`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Config Error: must HAVE at least one Input |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Config Error: Output Max less than or equal to Output Min, must be greater |
| **`P_HiLoSel`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 17 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_HiLoSel`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'High or Low Selector'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for display on HMI |
| **`Cfg_Inp1_Label`** | `STRING_20` | `'Input 1'` | [Biến cấu hình thông số] Input 1 Label for display on HMI |
| **`Cfg_Inp2_Label`** | `STRING_20` | `'Input 2'` | [Biến cấu hình thông số] Input 2 Label for display on HMI |
| **`Cfg_Inp3_Label`** | `STRING_20` | `'Input 3'` | [Biến cấu hình thông số] Input 3 Label for display on HMI |
| **`Cfg_Inp4_Label`** | `STRING_20` | `'Input 4'` | [Biến cấu hình thông số] Input 4 Label for display on HMI |
| **`Cfg_Inp5_Label`** | `STRING_20` | `'Input 5'` | [Biến cấu hình thông số] Input 5 Label for display on HMI |
| **`Cfg_Inp6_Label`** | `STRING_20` | `'Input 6'` | [Biến cấu hình thông số] Inpput 6 Label for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'High/Low Selector'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_NavTag`** | `STRING_NavTag[6]` | `{...}` | [Biến cấu hình thông số] Tagnames for destinations of Navigation buttons ([0]=input 1, ..., [5]=input 6) |
| **`Cfg_OutNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tagname for destination of output Navigation button |
| **`Cfg_Tag`** | `STRING_20` | `'P_HiLoSel'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`HMI_HasMore`** | `BOOL` | `0` | [Biến nội bộ giao diện HMI Faceplate] Tagname for display on HMI |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_HiLoSel'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_DBC`
**Thuộc chương:** Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_DBC`:** **105** Biến đầu vào (`Input Parameters`) | **82** Biến đầu ra (`Output Parameters`) | **37** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_DBC`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_PV`, `Inp_PVSrcQ`, `Inp_PVNotify`, `Inp_PVBad`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_OvrdRaiseSP`, `Inp_OvrdLowerSP`, `Inp_ExtInh`, `Inp_HiDevGate`, `Inp_LoDevGate`, `Inp_HiRoCIncGate`, `Inp_HiRoCDecGate`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_PVDecPlcs`, `Cfg_SetTrack`, `Cfg_HasMoreObj`, `Cfg_HasPVNav`, `Cfg_HasOutNav`, `Cfg_HasHiDevAlm`, `Cfg_HasLoDevAlm`, `Cfg_HasHiRoCIncAlm`, `Cfg_HasHiRoCDecAlm`, `Cfg_HiDevResetReqd`, `Cfg_LoDevResetReqd`, `Cfg_HiRoCIncResetReqd`, `Cfg_HiRoCDecResetReqd`, `Cfg_HiDevAckReqd`, `Cfg_LoDevAckReqd`, `Cfg_HiRoCIncAckReqd`, `Cfg_HiRoCDecAckReqd`, `Cfg_HiDevSeverity`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_OutNavTag`, `Cfg_PVEU`, `Cfg_PVNavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_DBC` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_LowerSP`, `PSet_RaiseSP`, `PSet_Owner`, `PCmd_Raise`, `PCmd_Lower`, `PCmd_None`, `PCmd_Auto`, `PCmd_Man`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_HiDevAck`, `PCmd_LoDevAck`, `PCmd_HiRoCIncAck`, `PCmd_HiRoCDecAck`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Raise`, `Out_Lower`, `Out_Q`, `Out_Qnot`, `Val_PV`, `Val_RoC`, `Val_LowerSP`, `Val_RaiseSP`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Raise`, `Sts_Lower`, `Sts_Auto`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 105 Biến Đầu Vào (`Input Parameters`) của `P_DBC`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_PV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Process Variable being controlled (EU) |
| **`Inp_PVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu đầu vào (nối từ chân SrcQ của khối P_AIChan nếu có). |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã trạng thái mức độ ưu tiên và xác nhận cảnh báo của đối tượng PV liên kết. |
| **`Inp_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=PV or I/O Comms Status Bad, 0=OK |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Override (higher priority program logic), 0=Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Loop Mode command: 0 = No command 1 = Energize Raise Output (in Manual) 2 = Energize Lower Output (in Manual) 3 = None 4 = Manual Loop mode 5 = Auto Loop mode |
| **`Inp_OvrdRaiseSP`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Mode Raise SP (EU) |
| **`Inp_OvrdLowerSP`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Mode Lower SP (EU) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_HiDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] High Deviation Status Gate, 1=enabled |
| **`Inp_LoDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Low Deviation Status Gate, 1=enabled |
| **`Inp_HiRoCIncGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] High Rate of Change (Increasing) Status Gate, 1=enabled |
| **`Inp_HiRoCDecGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] High Rate of Change (Decreasing) Status Gate, 1=enabled |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset all Alarms requiring reset |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên HMI (từ 0 đến 6 chữ số). |
| **`Cfg_SetTrack`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to set up bumpless behavior of setting parameters when switching modes. When this parameter is 1, in Program mode the operator settings track the program settings; in Operator mode the program settings track the operator settings; and the simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the operator settings and program settings are not modified by this instruction. In this case, when the mode is changed, the effective value of the setting can change depending on the program-set and operator-set values. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasPVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI to enable navigation to a connected PV object |
| **`Cfg_HasOutNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI to enable navigation to a connected output object |
| **`Cfg_HasHiDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasHiRoCIncAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasHiRoCDecAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HiDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiRoCIncResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiRoCDecResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiRoCIncAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiRoCDecAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiDevSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software, version 7.0, these severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_LoDevSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software, version 7.0, these severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_HiRoCIncSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software, version 7.0, these severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_HiRoCDecSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software, version 7.0, these severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] PV (Input) Range Minimum (EU) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] PV (Input) Range Maximum (EU) |
| **`Cfg_SPHiLim`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Setpoint High Limit (clamp) (EU) |
| **`Cfg_SPLoLim`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Setpoint Low Limit (clamp) (EU) |
| **`Cfg_RaiseDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Deadband for the Raise output (above Raise limit) |
| **`Cfg_LowerDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Deadband for the Lower output (below Lower limit) |
| **`Cfg_RateTime`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] PV Rate of Change time base (sec) 60=/min, 3600=/hr |
| **`Cfg_HiDevLim`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] High Deviation Status Threshold (EU) |
| **`Cfg_HiDevDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High Deviation Status Deadband (EU) |
| **`Cfg_LoDevLim`** | `REAL` | `-100` | Ẩn (Tag) | [Biến cấu hình thông số] Low Deviation Status Threshold (EU) |
| **`Cfg_LoDevDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Low Deviation Status Deadband (EU) |
| **`Cfg_HiDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above High Deviation Limit to raise Status (sec) |
| **`Cfg_HiDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below High Deviation Limit (minus deadband) to clear Status (sec) |
| **`Cfg_HiDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] High Deviation Status Gate Delay (sec) |
| **`Cfg_LoDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below Low Deviation Limit to raise Status (sec) |
| **`Cfg_LoDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above Low Deviation Limit (plus deadband) to clear Status (sec) |
| **`Cfg_LoDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Low Deviation Status Gate Delay (sec) |
| **`Cfg_HiRoCIncLim`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] High Rate of Change (Increasing) Status Threshold (EU/RateTime) |
| **`Cfg_HiRoCIncDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High Rate of Change (Increasing) Status Deadband (EU/RateTime) |
| **`Cfg_HiRoCIncOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above High Rate of Change (Increasing) Limit to raise Status (sec) |
| **`Cfg_HiRoCIncOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below High Rate of Change (Increasing) Limit (minus deadband) to clear Status (sec) |
| **`Cfg_HiRoCIncGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] High Rate of Change (Increasing) Status Gate Delay (sec) |
| **`Cfg_HiRoCDecLim`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] High Rate of Change (Decreasing) Status Threshold (EU/RateTime) |
| **`Cfg_HiRoCDecDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High Rate of Change (Decreasing) Status Deadband (EU/RateTime) |
| **`Cfg_HiRoCDecOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above High Rate of Change (Decreasing) Limit to raise Status (sec) |
| **`Cfg_HiRoCDecOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below High Rate of Change (Decreasing) Limit (minus deadband) to clear Status (sec) |
| **`Cfg_HiRoCDecGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] High Rate of Change (Decreasing) Status Gate Delay (sec) |
| **`PSet_LowerSP`** | `REAL` | `30` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting for Lower Setpoint (EU) |
| **`PSet_RaiseSP`** | `REAL` | `20` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting for Raise Setpoint (EU) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`XSet_LowerSP`** | `REAL` | `30` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting for Lower Setpoint (EU) |
| **`XSet_RaiseSP`** | `REAL` | `20` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting for Raise Setpoint (EU) |
| **`PCmd_Raise`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set output to Raise (in Manual) |
| **`PCmd_Lower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set output to Lower (in Manual) |
| **`PCmd_None`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để clear Raise, Lower outputs (in Manual) |
| **`PCmd_Auto`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Automatic Loop Mode |
| **`PCmd_Man`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Manual Loop Mode |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`PCmd_HiDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiRoCIncAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiRoCDecAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiDevSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) High Deviation Alarm |
| **`PCmd_HiDevUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) High Deviation Alarm |
| **`PCmd_LoDevSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Low Deviation Alarm |
| **`PCmd_LoDevUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Low Deviation Alarm |
| **`PCmd_HiRoCIncSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) High Rate of Change (Increasing) Alarm |
| **`PCmd_HiRoCIncUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) High Rate of Change (Increasing) Alarm |
| **`PCmd_HiRoCDecSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) High Rate of Change (Decreasing) Alarm |
| **`PCmd_HiRoCDecUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) High Rate of Change (Decreasing) Alarm |
| **`PCmd_HiDevUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoDevUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiRoCIncUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiRoCDecUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`XCmd_Raise`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set output to Raise (in Manual) |
| **`XCmd_Lower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set output to Lower (in Manual) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_None`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để clear Raise, Lower outputs (in Manual) |
| **`XCmd_Auto`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để select Automatic Loop Mode |
| **`XCmd_Man`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để select Manual Loop Mode |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |

## 3. Bảng chi tiết 82 Biến Đầu Ra (`Output Parameters`) của `P_DBC`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Output - System Defined Parameter |
| **`Out_Raise`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to drive PV to increase |
| **`Out_Lower`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to drive PV to decrease |
| **`Out_Q`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output turns ON at Raise SP, OFF at Lower SP |
| **`Out_Qnot`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output turns ON at Lower SP, OFF at Raise SP |
| **`Val_PV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Process Variable (EU) |
| **`Val_RoC`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Process Variable Rate of Change (EU/RateTime) |
| **`Val_LowerSP`** | `REAL` | `30` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Accepted value for Lower Setpoint (EU) |
| **`Val_RaiseSP`** | `REAL` | `20` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Accepted value for Raise Setpoint (EU) |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu của dải đo đơn vị kỹ thuật = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại của dải đo đơn vị kỹ thuật = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final PV source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Confirmed Sts 0=Q off, 1=Q on, 2=Above LowerSP, 3=Below LowerSP, 4-7=same, in Manual |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Loop Fault Status 0 = none 22 = Lo Deviation 23 = Hi Deviation 24 = Hi Rate of Change (decreasing) 25 = Hi Rate of Change (increasing) 34 = Configuration error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_Raise`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=PV below Raise SP, Out_Raise is on |
| **`Sts_Lower`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=PV above Lower SP, Out_Lower is on |
| **`Sts_Auto`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Current Loop Mode is Automatic |
| **`Sts_Man`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Current Loop Mode is Manual |
| **`Sts_Q`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Out_Q is ON (Out_Qnot is OFF) |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Loop available for manipulation in Prog Mode |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải kỹ thuật Cfg_PVEUMin bằng Cfg_PVEUMax |
| **`Err_Rate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Rate Time (use 1.0, 60.0, 3600.0, 86400.0...) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ On/Off/Gate Delay ngoài phạm vi hợp lệ (0..2147483 giây) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Alarm Min On Time or Severity |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Loop is Disabled (held with outputs off) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Mode is Maintenance (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Mode is Program |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Mode is Operator |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program or Operator has requested Mode Lock |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=NoMode (Disabled because EnableIn is False) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Deviation kết quả so sánh ngưỡng trực tiếp 1=High Dev |
| **`Sts_HiDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Deviation trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiDev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog Input Deviation đang vượt trên ngưỡng Cao (High) |
| **`Alm_HiDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Analog Input Deviation is in Cảnh báo Cao (High Alarm) |
| **`Ack_HiDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=High Deviation Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_HiDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Deviation Alarm is Disabled (by Maintenance) |
| **`Sts_HiDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Deviation Alarm is Shelved (by Operator) |
| **`Sts_HiDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Deviation Alarm is Suppressed (by Program) |
| **`Sts_LoDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low Deviation kết quả so sánh ngưỡng trực tiếp 1=Low Dev |
| **`Sts_LoDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low Deviation trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_LoDev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog Input Deviation đang tụt dưới ngưỡng Thấp (Low) |
| **`Alm_LoDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Analog Input Deviation is in Cảnh báo Thấp (Low Alarm) |
| **`Ack_LoDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Low Deviation Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_LoDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Deviation Alarm is Disabled (by Maintenance) |
| **`Sts_LoDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Deviation Alarm is Shelved (by Operator) |
| **`Sts_LoDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Deviation Alarm is Suppressed (by Program) |
| **`Sts_HiRoCIncCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Rate of Change (Increasing) kết quả so sánh ngưỡng trực tiếp 1=High RoC |
| **`Sts_HiRoCIncGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Rate of Change (Increasing) trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiRoCInc`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=PV Rate of Change (Increasing) đang vượt trên ngưỡng Cao (High) |
| **`Alm_HiRoCInc`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=PV Rate of Change (Increasing) is in Alarm |
| **`Ack_HiRoCInc`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=PV Rate of Change (Increasing) Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_HiRoCIncDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=PV Rate of Change (Increasing) Alarm is Disabled (by Maintenance) |
| **`Sts_HiRoCIncShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=PV Rate of Change (Increasing) Alarm is Shelved (by Operator) |
| **`Sts_HiRoCIncSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=PV Rate of Change (Increasing) Alarm is Suppressed (by Program) |
| **`Sts_HiRoCDecCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Rate of Change (Decreasing) kết quả so sánh ngưỡng trực tiếp 1=High |
| **`Sts_HiRoCDecGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Rate of Change (Decreasing) trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiRoCDec`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=PV Rate of Change (Decreasing) đang vượt trên ngưỡng Cao (High) |
| **`Alm_HiRoCDec`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=PV Rate of Change (Decreasing) is in Alarm |
| **`Ack_HiRoCDec`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=PV Rate of Change (Decreasing) Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_HiRoCDecDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=PV Rate of Change (Decreasing) Alarm is Disabled (by Maintenance) |
| **`Sts_HiRoCDecShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=PV Rate of Change (Decreasing) Alarm is Shelved (by Operator) |
| **`Sts_HiRoCDecSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=PV Rate of Change (Decreasing) Alarm is Suppressed (by Program) |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_DBC`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 37 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_DBC`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Deadband Controller'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'On/Off Controller'` | [Biến cấu hình thông số] Label for graphic symbol that is displayed on HMI. This string appears on the display element. |
| **`Cfg_OutNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tagname for destination of Output Navigation button. |
| **`Cfg_PVEU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for display on HMI |
| **`Cfg_PVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tagname for destination of PV Navigation button |
| **`Cfg_Tag`** | `STRING_20` | `'P_DBC'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`HiDev`** | `P_Alarm` | `{...}` | High Deviation Alarm |
| **`HiDevGate`** | `P_Gate` | `{...}` | High Deviation Alarm Gate / Delay Block |
| **`HiRoCDec`** | `P_Alarm` | `{...}` | High Rate of Change (Decreasing) Alarm |
| **`HiRoCDecGate`** | `P_Gate` | `{...}` | High Rate of Change (Decreasing) Alarm Gate / Delay Block |
| **`HiRoCInc`** | `P_Alarm` | `{...}` | High Rate of Change (Increasing) Alarm |
| **`HiRoCIncGate`** | `P_Gate` | `{...}` | High Rate of Change (Increasing) Alarm Gate / Delay Block |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `??` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`LoDev`** | `P_Alarm` | `{...}` | Low Deviation Alarm |
| **`LoDevGate`** | `P_Gate` | `{...}` | Low Deviation Alarm Gate / Delay Block |
| **`OCmd_Auto`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để select Automatic Loop Mode |
| **`OCmd_Lower`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set output to Lower (in Manual) |
| **`OCmd_Man`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để select Manual Loop Mode |
| **`OCmd_None`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để clear Raise, Lower outputs (in Manual) |
| **`OCmd_Raise`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set output to Raise (in Manual) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`ORdy_Auto`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Auto |
| **`ORdy_Lower`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Lower |
| **`ORdy_Man`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Man |
| **`ORdy_None`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_None |
| **`ORdy_Raise`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Raise |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SP`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh Setpoint OSets (enables data entry fields) |
| **`OSet_LowerSP`** | `REAL` | `30` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting for Lower Setpoint (EU) |
| **`OSet_RaiseSP`** | `REAL` | `20` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting for Raise Setpoint (EU) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Seq`
**Thuộc chương:** Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Seq`:** **100** Biến đầu vào (`Input Parameters`) | **96** Biến đầu ra (`Output Parameters`) | **95** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Seq`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_SeqLoopCnt`, `Inp_Started`, `Inp_Branch`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_ExtInh`, `Inp_Reset`, `Inp_PromptCfm`, `Inp_PromptRdy`, `Inp_PromptSelect`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasInp`, `Cfg_UseInp`, `Cfg_InpForceMask`, `Cfg_OutTrackMask`, `Cfg_OutRealTrackMask`, `Cfg_ScaleRealOut`, `Cfg_HoldOutState`, `Cfg_HoldOutMask`, `Cfg_HoldOutRealMask`, `Cfg_StopOutState`, `Cfg_StopOutMask`, `Cfg_StopOutRealMask`, `Cfg_IntlkTripAction`, `Cfg_StepFTmrDNAction`, `Cfg_QtyMin`, `Cfg_QtyMax`, `Cfg_StdQty`, `Cfg_SeqTO`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Desc`, `Cfg_HasInpNav`, `Cfg_HoldOutRealValue`, `Cfg_InpName`, `Cfg_InpNavTag`, `Cfg_Label`, `Cfg_OutName`, `Cfg_OutRealName`, `Cfg_QtyEU`, `Cfg_StopOutRealValue`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Seq` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PSet_Qty`, `PCmd_SeqStart`, `PCmd_SeqHold`, `PCmd_SeqRestart`, `PCmd_SeqStop`, `PCmd_SeqAbort`, `PCmd_SeqReset`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_StepTOAck`, `PCmd_SeqTOAck`, `PCmd_IntlkTripAck`, `PCmd_StepTOSuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_00…Out_31`, `Out_Real_00…Out_Real_31`, `Out_PromptReq`, `Out_PromptRef`, `Val_Qty`, `Val_QtyPct`, `Val_MaxStepIndex`, `Val_CurrStepNumAbs`, `Val_CurrStepNum`, `Val_SeqLoopCnt`, `Val_InSeqDay`, `Val_InSeqHrs`, `Val_InSeqMin`, `Val_InSeqSec`, `Val_InSeqACC`, `Val_InSeqTOPct`, `Val_InStepDay`, `Val_InStepHrs`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 100 Biến Đầu Vào (`Input Parameters`) của `P_Seq`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_SeqLoopCnt`** | `DINT` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Number of times to execute entire Sequence in a loop |
| **`Inp_Started`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=External logic / equipment ready for Sequence to execute |
| **`Inp_Branch`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Sequence should branch if configured to do so on Input, 0=don't branch |
| **`Inp_PermOK`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, sequence can start |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, sequence can start |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks OK, sequence can start/run |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Interlocks OK, sequence can start/run |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input parameter used to programatically reset alarms. When set to 1, all fault conditions and latched alarms are reset. |
| **`Inp_PromptCfm`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Manual Prompt has received Operator confirmation |
| **`Inp_PromptRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Manual Prompt has been posted to Operator |
| **`Inp_PromptSelect`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Integer response to Manual Prompt |
| **`Cfg_HasInp`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Bits=1 are Inputs configured to Exist (Engineering) |
| **`Cfg_UseInp`** | `DINT` | `2#1111_1111_1111_1111_1111_1111_1111_1111` | Ẩn (Tag) | [Biến cấu hình thông số] Bits=1 are Inputs configured to be Used (Maintenance) |
| **`Cfg_InpForceMask`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Bits=1 are Inputs available to be Forced by Maintenance |
| **`Cfg_OutTrackMask`** | `DINT` | `2#1111_1111_1111_1111_1111_1111_1111_1111` | Ẩn (Tag) | [Biến cấu hình thông số] Bits=1 are Boolean Outputs set for Tracking when Sequence not writing to them |
| **`Cfg_OutRealTrackMask`** | `DINT` | `2#1111_1111_1111_1111_1111_1111_1111_1111` | Ẩn (Tag) | [Biến cấu hình thông số] Bits=1 are Real Outputs set for Tracking when Sequence not writing to them |
| **`Cfg_ScaleRealOut`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Bits=1 are REAL Outputs that are scaled using Inp_ScaleFact |
| **`Cfg_HoldOutState`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] State of Outputs on Hold |
| **`Cfg_HoldOutMask`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Which Outputs to Set on Hold |
| **`Cfg_HoldOutRealMask`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Which Real Outputs to Write on Hold |
| **`Cfg_StopOutState`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] State of Outputs on Stop |
| **`Cfg_StopOutMask`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Which Outputs to Set on Stop |
| **`Cfg_StopOutRealMask`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Which Real Outputs to Write on Stop |
| **`Cfg_IntlkTripAction`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Interlock Trip action (enum) 0=Stop, 1=Hold, 2=Go to Intlk Step |
| **`Cfg_StepFTmrDNAction`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Step Timeout action (enum) 0=Alarm/Status only, 1=Hold |
| **`Cfg_QtyMin`** | `INT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Quantity Input Lower Limit |
| **`Cfg_QtyMax`** | `INT` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Quantity Input Upper Limit |
| **`Cfg_StdQty`** | `INT` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Quantity Input Standard (Nominal) Value (=100%) |
| **`Cfg_SeqTO`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Sequence Timeout time (ms) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasPrompt`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=This Sequence has an associated Manual Prompt object |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng Permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasStepTOAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasSeqTOAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_StepTOResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_SeqTOResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_StepTOAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_SeqTOAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_SeqTOSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_StepTOSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PSet_Qty`** | `INT` | `100` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Quantity Input Value |
| **`XSet_Qty`** | `INT` | `100` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Quantity Input Value |
| **`MSet_SingleStep`** | `BOOL` | `0` | Ẩn (Tag) | [Giá trị đặt từ tab Bảo trì (Maintenance)] Maintenance Setting to execute Single Step only: 0 = Normal, 1 = Single Step |
| **`PCmd_SeqStart`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Sequence |
| **`PCmd_SeqHold`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Hold Sequence |
| **`PCmd_SeqRestart`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Restart Sequence |
| **`PCmd_SeqStop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Stop Sequence |
| **`PCmd_SeqAbort`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Abort Sequence |
| **`PCmd_SeqReset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset Sequence |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset all Alarms requiring Reset |
| **`PCmd_StepTOAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_SeqTOAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_StepTOSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Step Timeout Alarm |
| **`PCmd_SeqTOSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Sequence Timeout Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_StepTOUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Step Timeout Alarm |
| **`PCmd_SeqTOUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Sequence Timeout Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_StepTOUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_SeqTOUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_SeqStart`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Sequence |
| **`OCmd_SeqHold`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Hold Sequence |
| **`OCmd_SeqRestart`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Restart Sequence |
| **`OCmd_SeqStop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Stop Sequence |
| **`OCmd_SeqAbort`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Abort Sequence |
| **`OCmd_SeqReset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset Sequence |
| **`OCmd_SeqPause`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Pause Sequence |
| **`OCmd_SeqResume`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Resume Sequence |
| **`OCmd_SeqAutoPause`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Auto-Pause Sequence |
| **`OCmd_SeqCancelPause`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Cancel Sequence Pause or AutoPause |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |
| **`XCmd_SeqStart`** | `BOOL` | - | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Người vận hành (Operator HMI) để Start Sequence |
| **`XCmd_SeqHold`** | `BOOL` | - | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Người vận hành (Operator HMI) để Hold Sequence |
| **`XCmd_SeqRestart`** | `BOOL` | - | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Người vận hành (Operator HMI) để Restart Sequence |
| **`XCmd_SeqStop`** | `BOOL` | - | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Người vận hành (Operator HMI) để Stop Sequence |
| **`XCmd_SeqAbort`** | `BOOL` | - | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Người vận hành (Operator HMI) để Abort Sequence |
| **`XCmd_SeqReset`** | `BOOL` | - | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Người vận hành (Operator HMI) để Reset Sequence |
| **`XCmd_SeqPause`** | `BOOL` | - | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Người vận hành (Operator HMI) để Pause Sequence |
| **`XCmd_SeqResume`** | `BOOL` | - | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Người vận hành (Operator HMI) để Resume Sequence |
| **`XCmd_SeqAutoPause`** | `BOOL` | - | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Người vận hành (Operator HMI) để Auto-Pause Sequence |
| **`XCmd_SeqCancelPause`** | `BOOL` | - | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Người vận hành (Operator HMI) để Cancel Sequence Pause or AutoPause |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Reset all Alarms requiring Reset |

## 3. Bảng chi tiết 96 Biến Đầu Ra (`Output Parameters`) của `P_Seq`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | - | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_00…Out_31`** | `BOOL` | - | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 32 Sequencer output bits. |
| **`Out_Real_00…Out_Real_31`** | `REAL` | - | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 32 Sequencer output REAL values. |
| **`Out_PromptReq`** | `BOOL` | - | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Manual Prompt should be posted to Operator |
| **`Out_PromptRef`** | `DINT` | - | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Link to which manual prompt (in array) |
| **`Val_Qty`** | `INT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Accepted value of PSet_ / OSet_Qty quantity entry |
| **`Val_QtyPct`** | `REAL` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Quantity As a Percent of Nominal Quantity |
| **`Val_MaxStepIndex`** | `DINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum allowed Step Index (= size of Ref_Steps array - 1) |
| **`Val_CurrStepNumAbs`** | `DINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Currently Running Step (Absolute) |
| **`Val_CurrStepNum`** | `DINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Currently Running Step |
| **`Val_SeqLoopCnt`** | `DINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Number of sequence loops completed |
| **`Val_InSeqDay`** | `INT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time since sequence was started: days in ddd hh:mm:ss |
| **`Val_InSeqHrs`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time since sequence was started: hours in ddd hh:mm:ss |
| **`Val_InSeqMin`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time since sequence was started: minutes in ddd hh:mm:ss |
| **`Val_InSeqSec`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time since sequence was started: seconds in ddd hh:mm:ss |
| **`Val_InSeqACC`** | `DINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in Sequence Timer Accumulator (msec) |
| **`Val_InSeqTOPct`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Percentage of elapsed time to Sequence Timeout (for bargraph) |
| **`Val_InStepDay`** | `INT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time since sequence was started: days in ddd hh:mm:ss |
| **`Val_InStepHrs`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in this step: hours in hh:mm:ss |
| **`Val_InStepMin`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in this step: minutes in hh:mm:ss |
| **`Val_InStepSec`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in this step: seconds in hh:mm:ss |
| **`Val_InStepACC`** | `DINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time In Step Timer Accumulator (msec) |
| **`Val_InStepTOPct`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Percentage of elapsed time to Sequence Timeout (for bargraph) |
| **`Val_StepTRemainHrs`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Step time remaining: hours in hh:mm:ss |
| **`Val_StepTRemainMin`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Step time remaining: minutes in hh:mm:ss |
| **`Val_StepTRemainSec`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Step time remaining: seconds in hh:mm:ss |
| **`Val_StepTRemain`** | `DINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Remaining Time Before Transition is Allowed |
| **`Val_State`** | `DINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Sequence state (enumeration): 0 = Idle 1 = Starting 2 = Running 3 = Complete 4 = Stopped 5 = Held |
| **`Val_Owner`** | `DINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Val_NotifyAll`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_SeqIdle`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence is Idle |
| **`Sts_SeqStarting`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence is Starting |
| **`Sts_SeqRunning`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence is Running |
| **`Sts_SeqHeld`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence is Held |
| **`Sts_SeqComplete`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence is Complete |
| **`Sts_SeqStopping`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence is Stopping |
| **`Sts_SeqStopped`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence is Stopped |
| **`Sts_SeqAborted`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence is Aborted |
| **`Sts_SeqPaused`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence is Paused |
| **`Sts_SeqExecuting`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Starting, Running or Holding |
| **`Sts_InpForced`** | `DINT` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Bits=1 are Inputs Forced by Maintenance |
| **`Sts_Qualified`** | `DINT` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Bits=1 are Qualified Inputs (in required state for transition) |
| **`Sts_TQualified`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Step Timer Qualified for current step |
| **`Sts_MQualified`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Manual Prompt Qualified for current step |
| **`Sts_Available`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Intlk`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_Perm`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_OoS`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Sts_MaintByp`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Alarm`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Alarm Min On Time or Severity |
| **`Err_StepSizeFault`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid number of steps in step array |
| **`Err_NextStep`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Next Step Number is outside array bounds |
| **`Err_QtyCfg`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Min, Max, Std Quantity Invalid |
| **`Err_Qty`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Entered Quantity Out of Range |
| **`Sts_OoS`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=The drive, or a latched alarm or shed condition, is ready to be reset |
| **`Sts_RdyAck`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_StepTO`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Step has timed out |
| **`Alm_StepTO`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Step Timeout Alarm |
| **`Ack_StepTO`** | `BOOL` | - | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Step Timeout Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_StepTODisabled`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Step Timeout Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì (inhibits new alarm) |
| **`Sts_StepTOShelved`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Step Timeout Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành (inhibits new alarm) |
| **`Sts_StepTOSuppressed`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Step Timeout Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC logic (inhibits new alarm) |
| **`Sts_SeqTO`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence has timed out |
| **`Alm_SeqTO`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Sequence Timeout Alarm |
| **`Ack_SeqTO`** | `BOOL` | - | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Sequence Timeout Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_SeqTODisabled`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence Timeout Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì (inhibits new alarm) |
| **`Sts_SeqTOShelved`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence Timeout Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành (inhibits new alarm) |
| **`Sts_SeqTOSuppressed`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence Timeout Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC logic (inhibits new alarm) |
| **`Sts_IntlkTrip`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Sequence stopped by an Interlock NOT OK (one-shot) |
| **`Alm_IntlkTrip`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Sequence stopped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | - | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì (inhibits new alarm) |
| **`Sts_IntlkTripShelved`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành (inhibits new alarm) |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC logic (inhibits new alarm) |
| **`MRdy_Rel`** | `BOOL` | - | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | - | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`x Ref_Steps`** | `P_SeqStep[2]` | - | Ẩn (Tag) | Tag containing list of Steps for this Sequence |
| **`P_Seq`** | `BOOL` | - | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 95 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Seq`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Simple Sequencer'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Simple Sequencer'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_HasInpNav`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | [Biến cấu hình thông số] Bits=1 indicate inputs with navigation enabled |
| **`Cfg_HoldOutRealValue`** | `REAL[32]` | `{...}` | [Biến cấu hình thông số] Real Values to output on Hold |
| **`Cfg_InpName`** | `STRING_20[32]` | `{...}` | [Biến cấu hình thông số] Real Values to output on Hold |
| **`Cfg_InpNavTag`** | `STRING_NavTag[32]` | `{...}` | [Biến cấu hình thông số] Navigation Tag |
| **`Cfg_Label`** | `STRING_20` | `'Simple Sequencer'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_OutName`** | `STRING_20[32]` | `{...}` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_OutRealName`** | `STRING_20[32]` | `{...}` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_QtyEU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_StopOutRealValue`** | `REAL[32]` | `{...}` | [Biến cấu hình thông số] Real Values to output on Stop |
| **`Cfg_Tag`** | `STRING_20` | `'P_Seq'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Command Source |
| **`Cur_EditState`** | `DINT` | `0` | Command Source |
| **`Cur_EditStep`** | `DINT` | `0` | Command Source |
| **`Cur_EditStepRel`** | `DINT` | `0` | Command Source |
| **`Cur_HasFaultTimer`** | `BOOL` | `0` | Command Source |
| **`Cur_HasTimer`** | `BOOL` | `0` | Command Source |
| **`Cur_Step`** | `P_SeqStep` | `{...}` | Data for one P_Seq sequencer Step |
| **`Cur_StepHasInp`** | `DINT` | `0` | Data for one P_Seq sequencer Step |
| **`Cur_StepQualifyState`** | `DINT` | `0` | Data for one P_Seq sequencer Step |
| **`Cur_TimerCfg`** | `BOOL` | `0` | Data for one P_Seq sequencer Step |
| **`HMI_CurrentPosition`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_EDitStepAbs`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_EDitStepNextAbs`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_EDitStepPrevAbs`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_HasSeqTO`** | `BOOL` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_HasStepTO`** | `BOOL` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_InMask`** | `DINT[11]` | `{...}` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_InState`** | `DINT[11]` | `{...}` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_InStepNum`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_InStepVis`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_Lib`** | `STRING_16` | `'RA-Seq'` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_Manual`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_MaxCntRealOut`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_NumStepsInEditState`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_NumStepsInState`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_Pause`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_QtyVis`** | `BOOL` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_RealOutVis`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Data for one P_Seq sequencer Step |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_TimerDeferred`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_TimerType`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_TimerUsed`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Seq'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`HMI_ViewFirstStep`** | `DINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MCmd_InpForce`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Maintenance Command (per bit) to force Input for Current Step |
| **`MCmd_SeqStepForce`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Force Transition out of Current Step |
| **`MCmd_TimerForce`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để force Step Timer done for Current Step |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_NextStep`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_NextStep (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_SeqStepForce`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SeqStepForce (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MSet_NextStep`** | `DINT` | `0` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Maintenance Setting to set next Step to execute |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions |
| **`OCmd_StdQtyReset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để reset OSet_Qty to Standard quantity (Cfg_StdQty) |
| **`OCmd_StepAdd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để reset OSet_Qty to Standard quantity (Cfg_StdQty) |
| **`OCmd_StepDel`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Operator entry for step number to delete |
| **`OCmd_StepIns`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Operator entry for step number to insert |
| **`ORdy_AddStep`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] Operator entry for step number to insert |
| **`ORdy_DeleteStep`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] Operator entry for step number to insert |
| **`ORdy_Edit`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Edit (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_InsertStep`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Edit (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Output`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | [Cờ cho phép sáng nút nhấn trên HMI] Bits=1=BOOL Outputs ready for manual Output commands |
| **`ORdy_Qty`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_Qty (enables data entry field) |
| **`ORdy_RealOutput`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | [Cờ cho phép sáng nút nhấn trên HMI] Bits=1=REAL Outputs ready for manual Output entries |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=At least one Alarm or Latched Shed requires Reset |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=At least one Alarm or latched Shed condition requires Reset or Ack |
| **`ORdy_SeqAbort`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqStop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SeqAutoPause`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqAutoPause (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SeqCancelPause`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqCancelPause (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SeqHold`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqHold (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SeqPause`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqPause (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SeqReset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqReset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SeqRestart`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqRestart (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SeqResume`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqResume (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SeqStart`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqStart (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SeqStop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SeqStop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_StdQtyReset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StdQtyReset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OSet_Qty`** | `INT` | `100` | [Giá trị đặt từ màn hình HMI Operator] Operator Quantity Input Value |
| **`Ref_States`** | `P_SeqSt[11]` | `{...}` | [Tham chiếu InOut nối tới cấu trúc ngoài] Data for one P_Seq State |
| **`SeqTO`** | `P_Alarm` | `{...}` | Alarm |
| **`StepTO`** | `P_Alarm` | `{...}` | Alarm |
| **`Sts_QDonut`** | `BOOL` | `0` | [Cờ báo trạng thái logic (Status)] Alarm |
| **`Sts_QDonutHole`** | `BOOL` | `0` | [Cờ báo trạng thái logic (Status)] Alarm |
| **`Val_CurrStepName`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Alarm |
| **`Val_InStepT2TO`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Remaining Time Before Step Faults on TO |
| **`Val_MinStepIndex`** | `SINT` | `1` | [Giá trị thực tế đầu ra (Logic/HMI)] Ref_Steps Array Size - 1 |
| **`Val_NextStepName`** | `STRING_16` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Ref_Steps Array Size - 1 |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Dose`
**Thuộc chương:** Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Dose`:** **117** Biến đầu vào (`Input Parameters`) | **95** Biến đầu ra (`Output Parameters`) | **41** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Dose`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_RatePV`, `Inp_QtyPV`, `Inp_RatePVSrcQ`, `Inp_QtyPVSrcQ`, `Inp_RatePVNotify`, `Inp_QtyPVNotify`, `Inp_RunFdbk`, `Inp_DribbleFdbk`, `Inp_StopFdbk`, `Inp_CtrldEqpFault`, `Inp_RatePVBad`, `Inp_RatePVUncertain`, `Inp_QtyPVBad`, `Inp_QtyPVUncertain`, `Inp_Sim`, `Inp_ExtInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_LossInQty`, `Cfg_HasDribble`, `Cfg_HasEqpFdbk`, `Cfg_UseEqpFdbk`, `Cfg_AutoAdjPreact`, `Cfg_ShedOnEqpFault`, `Cfg_CalcRate`, `Cfg_CalcQty`, `Cfg_HasChanObjRate`, `Cfg_HasChanObjQty`, `Cfg_SetTrack`, `Cfg_QtyDecPlcs`, `Cfg_RateDecPlcs`, `Cfg_HasMoreObj`, `Cfg_HasOverTolAlm`, `Cfg_HasUnderTolAlm`, `Cfg_HasZeroFaultAlm`, `Cfg_HasEqpFaultAlm`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_QtyEU`, `Cfg_RateEU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Dose` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_SP`, `PSet_DribbleQty`, `PSet_Preact`, `PSet_TolHi`, `PSet_TolLo`, `PSet_Owner`, `PCmd_ClearTot`, `PCmd_StartTot`, `PCmd_StopTot`, `PCmd_StartFlow`, `PCmd_StopFlow`, `PCmd_CheckTol`, `PCmd_Bump`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_RunFlow`, `Out_DribbleFlow`, `Out_StopFlow`, `Out_RunTot`, `Out_ClearTot`, `Val_Qty`, `Val_Rate`, `Val_Remain`, `Val_PctComplete`, `Val_SP`, `Val_DribbleQty`, `Val_Preact`, `Val_TolHi`, `Val_TolLo`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 117 Biến Đầu Vào (`Input Parameters`) của `P_Dose`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_RatePV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Flow Rate from Flowmeter (EU/time, see Cfg_RateTime) |
| **`Inp_QtyPV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Quantity from Weigh Scale or Flowmeter (EU or Pulse Count) |
| **`Inp_RatePVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Flow Rate signal source and quality (enum) |
| **`Inp_QtyPVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Quantity signal source and quality (enum) |
| **`Inp_RatePVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Rate PV object alarm prio and ack status (enum) |
| **`Inp_QtyPVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Quantity PV object alarm prio and ack status (enum) |
| **`Inp_RunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Controlled Equipment is Delivering (Running) |
| **`Inp_DribbleFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Controlled Equipment is Delivering at Dribble |
| **`Inp_StopFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Controlled Equipment is confirmed Stopped |
| **`Inp_CtrldEqpFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Controlled Equipment device or I/O status 0=OK, 1=fail |
| **`Inp_RatePVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Rate PV Input Quality = Bad (fail) |
| **`Inp_RatePVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Rate PV Input Quality = Uncertain |
| **`Inp_QtyPVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Quantity PV Input Quality = Bad (fail) |
| **`Inp_QtyPVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Quantity PV Input Quality = Uncertain |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction simulates a working dosing action. When set to 0, the instruction controls dosing normally. |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset all fault conditions and latched Alarms |
| **`Cfg_LossInQty`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Flow reduces quantity (Xfer Out), 0=Flow increases quantity (Xfer In) |
| **`Cfg_HasDribble`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Slow to Dribble before complete, 0=Run full flow until complete |
| **`Cfg_HasEqpFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Controlled Equipment provides Run (Dribble if used) and Stop Feedback |
| **`Cfg_UseEqpFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Use run / dribble / stop feedback, 0=Assume equipment state |
| **`Cfg_AutoAdjPreact`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Enable automatic adjustment of preact after each delivery |
| **`Cfg_ShedOnEqpFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Stop delivery and Alarm on Equipment Fault; 0=Alarm only on Equipment Fault |
| **`Cfg_CalcRate`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=differentiate Inp_Qty to get Rate, 0=use Inp_Rate |
| **`Cfg_CalcQty`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=integrate Inp_Rate to get Quantity, 0=use Inp_Qty |
| **`Cfg_HasChanObjRate`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a Channel object (AIChan, etc.) is used for Inp_RatePV |
| **`Cfg_HasChanObjQty`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a Channel object (AIChan, etc.) is used for Inp_QtyPV |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to set up bumpless behavior of setting parameters when switching modes. When this parameter is 1, in Program mode the operator settings track the program settings; in Operator mode the program settings track the operator settings; and the simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the operator settings and program settings are not modified by this instruction. In this case, when the mode is changed, the effective value of the setting can change depending on the program-set and operator-set values. |
| **`Cfg_QtyDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for Quantity display (0..6) |
| **`Cfg_RateDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for Rate display (0..6) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasOverTolAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasUnderTolAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasZeroFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasEqpFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_OverTolResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_UnderTolResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_ZeroFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_EqpFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_OverTolAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_UnderTolAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_ZeroFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_EqpFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_OverTolSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_UnderTolSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_ZeroFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_EqpFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_MaxQty`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum allowed quantity to deliver (setpoint) (EU) |
| **`Cfg_EUQtyMult`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate to Quantity EU Multiplier (e.g., Gal to Bbl.) |
| **`Cfg_Rollover`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Quantity Rollover (e.g., max count for pulse input) |
| **`Cfg_CountsPerEU`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Number of Counts in Inp_QtyPV which equal 1.0 EU |
| **`Cfg_RateTime`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Time factor for rate (60 for /min, 3600 for /hr) (sec) |
| **`Cfg_RateFiltTC`** | `REAL` | `0.1` | Ẩn (Tag) | [Biến cấu hình thông số] Filter Time Constant (sec) for calculated Rate |
| **`Cfg_LoRateCutoff`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Rate below which to report zero flow (Inp_RatePV untis) |
| **`Cfg_SettleT`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Time to allow flow to stop before allowing tolerance check (sec) |
| **`Cfg_BumpT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Bump (manual top-off) Time (sec) |
| **`Cfg_AutoAdjPct`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Percentage of delivery error to auto-adjust preact (%) |
| **`Cfg_SimRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate at which to deliver when running in Simulation (EU/rate time) |
| **`Cfg_SimDribbleRate`** | `REAL` | `0.1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate at which to dribble when running in Simulation (EU/rate time) |
| **`Cfg_ClearPulseT`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Out_Clear to clear external totalizer (sec) |
| **`Cfg_FaultT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time for Equipment Feedback to follow Output before Fault (sec) |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Operator keeps control in Program command source: Bit .0 = Operator keeps Start/stop Bit .1 = Operator keeps Setpoint Bit .2 = Operator keeps Dribble/preact Bit .3 = Operator keeps Tolerances |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Program keeps control in Operator command source: Bit .0 = Program keeps Start/stop Bit .1 = Program keeps Setpoint Bit .2 = Program keeps Dribble/preact Bit .3 = Program keeps Tolerances |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] External keeps control: Bit .0 = External keeps Start/stop Bit .1 = External keeps Setpoint Bit .2 = External keeps Dribble/preact Bit .3 = External keeps Tolerances |
| **`PSet_SP`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of total quantity to deliver (EU) |
| **`PSet_DribbleQty`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of quantity to dribble (EU) |
| **`PSet_Preact`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of amount before total to stop flow (EU) |
| **`PSet_TolHi`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of High Tolerance Limit (OK amount > SP) |
| **`PSet_TolLo`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of Low Tolerance Limit (OK amount < SP) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`XSet_SP`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of total quantity to deliver (EU) |
| **`XSet_DribbleQty`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of quantity to dribble (EU) |
| **`XSet_Preact`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of amount before total to stop flow (EU) |
| **`XSet_TolHi`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of High Tolerance Limit (OK amount > SP) |
| **`XSet_TolLo`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of Low Tolerance Limit (OK amount < SP) |
| **`PCmd_ClearTot`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Totalizer Qty |
| **`PCmd_StartTot`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Totalizer |
| **`PCmd_StopTot`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Stop Totalizer |
| **`PCmd_StartFlow`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Delivery |
| **`PCmd_StopFlow`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Stop/Pause Delivery |
| **`PCmd_CheckTol`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Check Tolerances |
| **`PCmd_Bump`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] When PCmd_Bump is set to 1: • If the configured bump time (Cfg_BumpT) is non-zero, then bump delivery for the configured bump time – PCmd_Bump is cleared automatically • If the configured bump time (Cfg_BumpT) is zero, then bump delivery until PCmd_Bump is returned to 0 – PCmd_Bump is not cleared automatically |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_OverTolAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_UnderTolAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_ZeroFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_EqpFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_OverTolSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Out of Tolerance Cảnh báo Cao (High Alarm) |
| **`PCmd_UnderTolSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Out of Tolerance Cảnh báo Thấp (Low Alarm) |
| **`PCmd_ZeroFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Zero Fault Alarm |
| **`PCmd_EqpFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Equipment Fault Alarm |
| **`PCmd_OverTolUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Out of Tolerance Cảnh báo Cao (High Alarm) |
| **`PCmd_UnderTolUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Out of Tolerance Cảnh báo Thấp (Low Alarm) |
| **`PCmd_ZeroFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Zero Fault Alarm |
| **`PCmd_EqpFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Equipment Fault Alarm |
| **`PCmd_OverTolUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_UnderTolUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_ZeroFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_EqpFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |
| **`XCmd_ClearTot`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Clear Totalizer Qty |
| **`XCmd_StartTot`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Start Totalizer |
| **`XCmd_StopTot`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Stop Totalizer |
| **`XCmd_StartFlow`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] External command to Start Deliver |
| **`XCmd_StopFlow`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Stop/Pause Delivery |
| **`XCmd_CheckTol`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Check Tolerances |
| **`XCmd_Bump`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Bump Delivery for Under Tolerance |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Ext Cmd to clear shed latches and cleared alarms |

## 3. Bảng chi tiết 95 Biến Đầu Ra (`Output Parameters`) của `P_Dose`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | - | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_RunFlow`** | `BOOL` | - | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Deliver at Full (fast) Flow |
| **`Out_DribbleFlow`** | `BOOL` | - | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Deliver at Dribble (slow) Flow |
| **`Out_StopFlow`** | `BOOL` | - | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Stop Delivery Equipment |
| **`Out_RunTot`** | `BOOL` | - | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Run external totalizer (e.g. onboard flowmeter) |
| **`Out_ClearTot`** | `BOOL` | - | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Reset external totalizer (e.g. onboard flowmeter) |
| **`Val_Qty`** | `REAL` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Quantity actually delivered (totalizer output) (EU) |
| **`Val_Rate`** | `REAL` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Delivery Rate (EU/time) (see Cfg_RateTime) |
| **`Val_Remain`** | `REAL` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Amount yet to deliver to reach Setpoint (EU) |
| **`Val_PctComplete`** | `REAL` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Percent Complete (for Progress Bar on HMI) 0 to 100.00% |
| **`Val_SP`** | `REAL` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Amount to be delivered (Setpoint) (EU) |
| **`Val_DribbleQty`** | `REAL` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Amount to be delivered at slow rate (EU) |
| **`Val_Preact`** | `REAL` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Amount before SP at which flow will be stopped (EU) |
| **`Val_TolHi`** | `REAL` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Allowed Amount > SP (EU) |
| **`Val_TolLo`** | `REAL` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Allowed Amount < SP (EU) |
| **`SrcQ_IO`** | `SINT` | - | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | - | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final dose source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] This shows the command being processed, returns to zero once the command succeeds or fails. These values are also used for Inp_OvrdCmd: 0 = None 1 = Clear totalizer 2 = Start totalizer 3 = Start delivery 4 = Start dribble 5 = Bump 6 = Stop flow 7 = Stop totalizer |
| **`Val_Fdbk`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] This shows the feedback input from the device: 0 = None 1 = Stopped 2 = Delivering 3 = Dribbling |
| **`Val_Sts`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] This is the primary status, used to show the operator the confirmed device status: 0 = Power up/reset 1 = Stopped 2 = Totalizing 3 = Delivering 4 = Dribbling 5 = Bumping 6 = Start flow 7 = Start dribble 8 = Stopping 33 = Disabled |
| **`Val_Fault`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0=None, 17=EquipmentFault, 34=CfgErr |
| **`Val_NotifyAll`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `SINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | - | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_Cleared`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Totalizer Clear completed |
| **`Sts_TotRunning`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Totalizer Running, 0=Totalizer Stopped |
| **`Sts_FlowStarting`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Flow is Starting (Out_Run is ON, Fdbk not showing Run) |
| **`Sts_FlowRunning`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Flow is Running (Out_Run is ON, Fdbk shows Running) |
| **`Sts_DribbleStarting`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Dribble Starting (Out_Dribble is ON, Fdbk not showing Dribble) |
| **`Sts_FlowDribble`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Flow is Dribbling (Out_Dribble is ON, Fdbk shows Dribble) |
| **`Sts_FlowStopping`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Flow is Stopping (Out_Stop is ON, Fdbk not showing Stopped) |
| **`Sts_FlowStopped`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Flow is Stopped (Out_Stop is ON, Fdbk shows Stopped) |
| **`Sts_Bumping`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bump flow is active |
| **`Sts_LoRateCutoff`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Rate PV below low rate cutoff, flow assumed to be zero |
| **`Sts_Complete`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Total Delivered > (SP - Preact) |
| **`Sts_InTol`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Total Delivered is Within Tolerances |
| **`Sts_Available`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Dosing available for control by automation (Prog) |
| **`Sts_QtyBad`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Quantity Value is Bad (PV Fail) |
| **`Sts_QtyUncertain`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Quantity Value is Uncertain |
| **`Sts_RateBad`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Rate Value is Bad (PV Fail) |
| **`Sts_RateUncertain`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Rate Value is Uncertain |
| **`Sts_NotRdy`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_PVBad`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: PV Bad Quality or Comm Failure |
| **`Nrdy_EqpFault`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: External Equipment Fault (Fault or Shed Requires Reset) |
| **`Nrdy_OoS`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Not Ready: Out of Service |
| **`Sts_MaintByp`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Error: Bad Configuration, see detail Err bits for reason |
| **`Err_EU`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error: Cfg_CountsPerEU, Cfg_EUQtyMult or Cfg_Rollowver invalid |
| **`Err_Rate`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error: Cfg_RateFiltTC or Cfg_RateTime invalid |
| **`Err_Cutoff`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error: Cfg_LoRateCutoff invalid |
| **`Err_Limit`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error: Cfg_MaxQty invalid |
| **`Err_Sim`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error: Cfg_SimDribbleRate or Cfg_SimRate invalid |
| **`Err_Timer`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error: Cfg_BumpT, Cfg_FaultT or Cfg_ClearPulseT (use 0 to 2147483.647) |
| **`Err_Alarm`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_OoS`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Selection equals the Normal (Prog or Oper) |
| **`Sts_ProgReqInh`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_OverTol`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Delivery Out of Tolerance High |
| **`Alm_OverTol`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Delivery Out of Tolerance Cảnh báo Cao (High Alarm) |
| **`Ack_OverTol`** | `BOOL` | - | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Out of Tolerance Cảnh báo Cao (High Alarm) đã được Xác nhận (Acknowledged) |
| **`Sts_OverTolDisabled`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Out of Tolerance Cảnh báo Cao (High Alarm) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_OverTolShelved`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Out of Tolerance Cảnh báo Cao (High Alarm) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_OverTolSuppressed`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Out of Tolerance Cảnh báo Cao (High Alarm) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_UnderTol`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Delivery Out of Tolerance Low |
| **`Alm_UnderTol`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Delivery Out of Tolerance Cảnh báo Thấp (Low Alarm) |
| **`Ack_UnderTol`** | `BOOL` | - | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Out of Tolerance Cảnh báo Thấp (Low Alarm) đã được Xác nhận (Acknowledged) |
| **`Sts_UnderTolDisabled`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Out of Tolerance Cảnh báo Thấp (Low Alarm) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_UnderTolShelved`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Out of Tolerance Cảnh báo Thấp (Low Alarm) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_UnderTolSuppressed`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Out of Tolerance Cảnh báo Thấp (Low Alarm) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_ZeroFault`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Total did not clear or unexpected flow |
| **`Alm_ZeroFault`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Warning: Total did not clear or still flowing |
| **`Ack_ZeroFault`** | `BOOL` | - | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Zero Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_ZeroFaultDisabled`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Zero Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_ZeroFaultShelved`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Zero Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_ZeroFaultSuppressed`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Zero Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_EqpFault`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Equipment Fault Detected |
| **`Alm_EqpFault`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Equipment Fault Alarm |
| **`Ack_EqpFault`** | `BOOL` | - | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Equipment Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_EqpFaultDisabled`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Equipment Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_EqpFaultShelved`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Equipment Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_EqpFaultSuppressed`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Equipment Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | - | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | - | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_Dose`** | `BOOL` | - | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 41 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Dose`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `area01'` | [Biến cấu hình thông số] Process Area for security Read/Write |
| **`Cfg_Desc`** | `STRING_40` | `Dosing with Weigh Scale or Flowmeter'` | [Biến cấu hình thông số] Description for display on HMI Read/Write |
| **`Cfg_Label`** | `STRING_20` | `Dosing'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI Read/Write |
| **`Cfg_QtyEU`** | `STRING_8` | `'kg'` | [Biến cấu hình thông số] Engineering Units for Quantity for display on HMI Read/Write |
| **`Cfg_RateEU`** | `STRING_8` | `'kg/sec'` | [Biến cấu hình thông số] Engineering Units for Rate (flow) for display on HMI Read/Write |
| **`Cfg_Tag`** | `STRING_20` | `'P_Dose'` | [Biến cấu hình thông số] Tagname for display on HMI Read/Write |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển Read/Write |
| **`EqpFault`** | `P_Alarm` | `{...}` | Equipment (Sensor, Controlled Equip.) Fault Alarm Read/Write |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate Read Only |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Decimal Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) Read/Write |
| **`HMI_Type`** | `STRING_16` | `'P_Dose'` | [Biến nội bộ giao diện HMI Faceplate] 'P_Dose' Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin Read Only |
| **`OCmd_Bump`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Bump Delivery for Under Tolerance Read/Write |
| **`OCmd_CheckTol`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Check Tolerances Read/Write |
| **`OCmd_ClearTot`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Clear Totalizer Qty Read/Write |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset Read/Write |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions Read/Write |
| **`OCmd_StartFlow`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Deliver Read/Write |
| **`OCmd_StartTot`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Totalizer Read/Write |
| **`OCmd_StopFlow`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Stop/Pause Delivery Read/Write |
| **`OCmd_StopTot`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Stop Totalizer Read/Write |
| **`ORdy_Bump`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Bump (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_CheckTol`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_CheckTol (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_ClearTot`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ClearTot (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_DribPre`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_DribbleQty and OSet_Preact (enables HMI entry fields) Read Only |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_SP`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_SP (enables HMI entry field) Read Only |
| **`ORdy_StartFlow`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFlow (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_StartTot`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartTot (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_StopFlow`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StopFlow (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_StopTot`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StopTot (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_Tol`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSetTolHi, OSet_TolLo (enables HMI entry fields) Read Only |
| **`OSet_DribbleQty`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of quantity to dribble (EU) Read/Write |
| **`OSet_Preact`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of amount before total to stop flow (EU) Read/Write |
| **`OSet_SP`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of total quantity to deliver (EU) Read/Write |
| **`OSet_TolHi`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of High Tolerance Limit (OK amount > SP) Read/Write |
| **`OSet_TolLo`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of Low Tolerance Limit (OK amount < SP) Read/Write |
| **`OverTol`** | `P_Alarm` | `{...}` | Quantity Above High Tolerance Alarm Read/Write |
| **`UnderTol`** | `P_Alarm` | `{...}` | Quantity Below Low Tolerance Alarm Read/Write |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) Read Only |
| **`ZeroFault`** | `P_Alarm` | `{...}` | Not Zero when Expected Alarm Read/Write |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_LLS`
**Thuộc chương:** Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_LLS`:** **75** Biến đầu vào (`Input Parameters`) | **71** Biến đầu ra (`Output Parameters`) | **34** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_LLS`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdDemand`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_NumMotors`, `Cfg_MaxDemand`, `Cfg_MinDemand`, `Cfg_FirstOnFirstOff`, `Cfg_AllowRotate`, `Cfg_RotateOnStop`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasMoreObj`, `Cfg_HasNav`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_HasCantStartAlm`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_LLS` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Demand`, `PSet_Owner`, `PCmd_Start`, `PCmd_Stop`, `PCmd_Rotate`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_CantStartAck`, `PCmd_CantStopAck`, `PCmd_IntlkTripAck`, `PCmd_CantStartSuppress`, `PCmd_CantStopSuppress`, `PCmd_IntlkTripSuppress`, `PCmd_CantStartUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val_Demand`, `Val_RotateRank`, `Val_RotateID`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Stopped`, `Sts_Running`, `Sts_Stopping`, `Sts_Increasing`, `Sts_Decreasing`, `Sts_Available`, `Sts_Bypass`, `Sts_BypActive`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 75 Biến Đầu Vào (`Input Parameters`) của `P_LLS`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Enable Input - System Defined Parameter Read Only 0 |
| **`Ref_Motors`** | `P_LLS_Motor[2]` | `1` | Ẩn (Tag) | [Tham chiếu InOut nối tới cấu trúc ngoài] Motor Interface Array (link to 2 to 30 motors) 0 |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, Group can start Read/Write 0 |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, Group can start Read/Write 0 |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks OK, Group can start/run Read/Write 0 |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Interlocks OK, Group can start/run Read/Write 0 |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Hand (typ. hardwired local), 0=Release Hand Read/Write 0 |
| **`Inp_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Override (higher priority program logic), 0=Release Override Read/Write 0 |
| **`Inp_OvrdDemand`** | `DINT` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Override Mode setting for number of motors to run (MinDemand..MaxDemand) Read/Write 0 |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Override Mode Command: 0=None, 1=Stop Group, 2=Start Group, 3=Rotate Assignments Read/Write 0 |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition Read/Write 0 |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset all fault conditions and latched Alarms Read/Write 0 |
| **`Cfg_NumMotors`** | `DINT` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Number of motors in this Lead / Lag / Standby Group (2…30) IMPORTANT: If the Ref_Motors interface array has fewer elements than the entered value, this value will be reduced to the size of the array. |
| **`Cfg_MaxDemand`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum number of motors to run (1..NumMotors) Read/Write 0 |
| **`Cfg_MinDemand`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum number of motors to run (0..MaxDemand) Read/Write 0 |
| **`Cfg_FirstOnFirstOff`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=First started is first stopped, 0=First started is last stopped Read/Write 0 |
| **`Cfg_AllowRotate`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Allow Rotate ("cycle lead") command to rotate motor assignments Read/Write 0 |
| **`Cfg_RotateOnStop`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Rotate (cycle lead to end of list) upon stopping all motors Read/Write 0 |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasNav`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Set bits indicate which Motor Navigation buttons are enabled Read/Write 0 |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to configure bumpless behavior of setting parameters when switching modes. When this parameter is 1: • In Program command source, the operator settings track the program settings. • In Operator command source, the program settings track the operator settings. • The simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the instruction does not modify operator settings and program settings. In this case, when the mode is changed, the effective value of the setting can change depending on the program-set and operator-set values. |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Prog/Oper Settings track Override/Hand speed reference Read/Write 0 |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=OCmd_Stop any time, 0=OCmd_Stop only when Oper selected Read/Write 0 |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=XCmd_Stop any time, 0=XCmd_Stop only when Ext selected Read/Write 0 |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New group OCmd resets shed latches and cleared alarms, 0=OCmdReset req'd. Read/Write 0 |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New group XCmd resets shed latches and cleared alarms, 0=OCmdReset req'd. Read/Write 0 |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Override ignores Bypassable Perm/ Intlk; 0=always use Perm/Intlk Read/Write 0 |
| **`Cfg_HasCantStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasCantStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_CantStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_CantStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_CantStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_CantStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_CantStartSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_CantStopSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_StartDelay`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time (sec) after start or stop until next start is allowed (0..2M sec) Read/Write 0 |
| **`Cfg_StopDelay`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time (sec) after start or stop until next stop is allowed (0..2M sec) Read/Write 0 |
| **`PSet_Demand`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting for number of motors to run (MinDemand.. MaxDemand) Read/Write 0 |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). Read/Write 0 |
| **`XSet_Demand`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting for number of motors to run (MinDemand.. MaxDemand) Read/Write 0 |
| **`PCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) động cơ Group Read/Write 0 |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) động cơ Group Read/Write 0 |
| **`PCmd_Rotate`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Rotate Assignments (cycle lead to end of list) Read/Write 0 |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) Read/Write 0 |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) Read/Write 0 |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) Read/Write 0 |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) Read/Write 0 |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) Read/Write 0 |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để reset all alarms that require reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_CantStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_CantStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_CantStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Not Available to Start Alarm Read/Write 0 |
| **`PCmd_CantStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Not Available to Stop Alarm Read/Write 0 |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm Read/Write 0 |
| **`PCmd_CantStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Not Available to Start Alarm Read/Write 0 |
| **`PCmd_CantStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Not Available to Stop Alarm Read/Write 0 |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm Read/Write 0 |
| **`PCmd_CantStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_CantStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) Read/Write 0 |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) Read/Write 0 |
| **`XCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) động cơ Group Read/Write 0 |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) động cơ Group Read/Write 0 |
| **`XCmd_Rotate`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Rotate Assignments (cycle lead to end of list) Read/Write 0 |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Reset all Alarms requiring Reset Read/Write 0 |

## 3. Bảng chi tiết 71 Biến Đầu Ra (`Output Parameters`) của `P_LLS`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Output - System Defined Parameter |
| **`Val_Demand`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Number of motors requested to run |
| **`Val_RotateRank`** | `DINT` | `-1` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor rank (0=Lead, etc.) which will be demoted on Rotate |
| **`Val_RotateID`** | `DINT` | `-1` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor number which will be demoted on Rotate |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Group command: 0 = None 1 = Stop 2 = Start |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Group Feedback 0..31=Number of motors actually running |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Group Confirmed Status: 0 = Powerup/Unknown 1 = Group Stopped 2 = Group Running 7 = Group Stopping (commanded to stop, waiting for motors to stop) 14 = Group Running and Demand Decreasing (waiting for a motor to stop) 15 = Group Running and Demand Increasing (waiting for a motor to start) 33 = Group Disabled |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Group Fault Status: 0 = None 12 = Can't Start enough motors to meet demand 13 = Can't Stop enough motors to meet demand 34 = Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Highest Alarm prio and ack status this object + motors (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Motor Group requested to stop and all motors confirmed stopped |
| **`Sts_Running`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Motor Group requested to run |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Motor Group requested to stop and not all motors confirmed stopped |
| **`Sts_Increasing`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Group is starting motors in sequence to get up to demand |
| **`Sts_Decreasing`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Group is stopping motors in sequence to get down to demand |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Group available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Group Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Group Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Group Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Group Not Ready: Group is Out of Service |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Group Not Ready: Oper/Ext Priority Stop Requires Reset |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Group Not Ready: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Start/Stop Check timer preset (use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Alarm Throttle Time or Severity |
| **`Sts_MotorAvail`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Set bits indicate which motor are avialble for Program control |
| **`Sts_MotorStopped`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Set bits indicate which motors are confirmed Stopped |
| **`Sts_MotorStarting`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Set bits indicate which motors are Starting |
| **`Sts_MotorRunning`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Set bits indicate which motors are confirmed Running |
| **`Sts_MotorStopping`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Set bits indicate which motors are confirmed Stopped |
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
| **`Sts_CantStart`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Motor Failed to Start (one-shot) Read/Write 0 |
| **`Alm_CantStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Motor Fail to Start Alarm Read/Write 0 |
| **`Ack_CantStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Fail to Start Alarm đã được Xác nhận (Acknowledged) Read/Write 0 |
| **`Sts_CantStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Start Alarm has been disabled (will not be sent) Read/Write 0 |
| **`Sts_CantStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Start Alarm has been inhibited by logic Read/Write 0 |
| **`Sts_CantStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Start Alarm has been suppressed (logged only) Read/Write 0 |
| **`Sts_CantStop`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Motor Failed to Stop Read/Write 0 |
| **`Alm_CantStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Motor Fail to Stop Alarm Read/Write 0 |
| **`Ack_CantStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Fail to Stop Alarm đã được Xác nhận (Acknowledged) Read/Write 0 |
| **`Sts_CantStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Stop Alarm has been disabled (will not be sent) Read/Write 0 |
| **`Sts_CantStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Stop Alarm has been inhibited by logic Read/Write 0 |
| **`Sts_CantStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Stop Alarm has been suppressed (logged only) Read/Write 0 |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Motor stopped by an Interlock NOT OK (one-shot) Read/Write 0 |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Motor stopped by an Interlock NOT OK Read/Write 0 |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Interlock Trip Alarm đã được Xác nhận (Acknowledged) Read/Write 0 |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm is Disabled (not saved or sent) Read/Write 0 |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm has been inhibited by logic Read/Write 0 |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm has been suppressed (logged only) Read/Write 0 |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_LLS`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI Read/Write 0 |

## 4. Bảng chi tiết 34 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_LLS`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`CantStart`** | `P_Alarm` | `{...}` | Alarm: cannot start enough motors to meet demand Read/Write |
| **`CantStop`** | `P_Alarm` | `{...}` | Alarm: cannot stop enough motors to meet demand Read/Write |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security Read/Write |
| **`Cfg_Desc`** | `STRING_40` | `'Lead` | [Biến cấu hình thông số] / Lag / Standby Motor Group' Description for display on HMI Read/Write |
| **`Cfg_Label`** | `STRING_20` | `'Motor` | [Biến cấu hình thông số] Group Control' Label for graphic symbol displayed on HMI Read/Write |
| **`Cfg_Tag`** | `STRING_20` | `'P_LLS'` | [Biến cấu hình thông số] Tagname for display on HMI Read/Write |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển Read/Write |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate Read Only |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) Read/Write |
| **`HMI_Type`** | `STRING_16` | `'P_LLS'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate Read Only |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm Read/Write |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives Read/Write |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives Read/Write |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`MSet_MotorOoS`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Set bits indicate which motors have been taken Out of Service by Maintenance Read/Write |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset Read/Write |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions Read/Write |
| **`OCmd_Rotate`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Rotate Assignments (cycle lead to end of list) Read/Write |
| **`OCmd_Start`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) động cơ Group Read/Write |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) động cơ Group Read/Write |
| **`ORdy_Demand`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_Demand (enables numeric entry) Read Only |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_Rotate`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Rotate (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_Start`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Start (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`OSet_Demand`** | `DINT` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting for number of motors to run (MinDemand.. MaxDemand) Read/Write |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) Read Only |
| **`Val_PrefList`** | `DINT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Rank list of motor preferences: [0]=lead, [1]=lag, etc. Read Only |
| **`Val_PrioList`** | `DINT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Rank list of motor priorities: [0]=lead, [1]=lag, etc. Read Only |
| **`Val_RankList`** | `DINT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Rank list of motor numbers: [0]=lead, [1]=lag, etc.) Read Only |
| **`Val_StsList`** | `DINT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Rank list of motor status: [0]=lead, [1]=lag, etc.; .0=Avaiable, .1=Stopped, .2=Starting, .3=Running, .4=Stopping, .5=Out of Service (Maint) Read Only |
| **`Val_UsrList`** | `INT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Rank list of user sort criteria: [0]=lead, [1]=lag, etc. Read Only |

