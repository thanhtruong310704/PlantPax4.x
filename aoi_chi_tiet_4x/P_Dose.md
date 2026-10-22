# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Dose`
**Thuộc chương:** Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Dose`:** **117** Biến đầu vào (`Input Parameters`) | **95** Biến đầu ra (`Output Parameters`) | **41** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_Dose`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_DoseFM_4.10.00_AOI.L5X` (Flowmeter) / `P_DoseWS_4.10.00_AOI.L5X` (Weigh Scale) *(Trang 99–106 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Định lượng chiết rót chính xác (Dosing / Batch Weighing)** gồm 2 biến thể: dùng đồng hồ lưu lượng (`P_DoseFM`) hoặc dùng cân Loadcell (`P_DoseWS`). Điều khiển van nạp 2 cấp (**Nạp nhanh Bulk + Nạp tinh Dribble**), tự động đóng sớm để **bù lượng nguyên liệu còn đang rơi trên đường ống (`Pre-Act / Slop`)** và kiểm tra rò rỉ van.
- **Các nhu cầu thực tế CẦN dùng khối `P_Dose`:** Khi cần **nạp chính xác một lượng Kg hoặc Lít hóa chất, nước, dầu ăn, hương liệu, phụ gia vào bồn trộn** mà không cần viết logic cắt sớm phức tạp.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi chỉ cần đọc lưu lượng cộng dồn liên tục không điều khiển đóng/mở van nạp mẻ.

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
