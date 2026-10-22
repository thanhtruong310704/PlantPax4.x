# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PIDE`
**Thuộc chương:** Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_PIDE`:** **218** Biến đầu vào (`Input Parameters`) | **149** Biến đầu ra (`Output Parameters`) | **59** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_PIDE`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_PIDE_4.10.00_AOI.L5X` *(Trang 79–83 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối điều khiển vòng kín **PID nâng cao** chuẩn PlantPAx (bao bọc lệnh `PIDE` của Logix). Hỗ trợ điều khiển Đơn vòng, Tầng (**Cascade**), Tỷ lệ (**Ratio**), Bù nhiễu tiến cử (**Feedforward**), Tự động dò thông số (**Autotune**), 4 cảnh báo sai lệch (`HiHiDev`..`LoLoDev`), Setpoint Ramping và cưỡng bức CV về giá trị an toàn khi mất liên động.
- **Các nhu cầu thực tế CẦN dùng khối `P_PIDE`:** Cho **mọi bài toán điều khiển liên tục (Continuous Control)** cần giữ ổn định Nhiệt độ, Áp suất, Lưu lượng, Mức bồn, pH, Oxy hòa tan bằng van tuyến tính hoặc biến tần.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi điều khiển đóng/ngắt 2 vị trí theo khoảng trễ (như bơm hố thu) → Dùng [`P_DBC.md`](./P_DBC.md).

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
