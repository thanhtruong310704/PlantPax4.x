# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AInHART`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_AInHART`:** **107** Biến đầu vào (`Input Parameters`) | **81** Biến đầu ra (`Output Parameters`) | **54** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_AInHART`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_AInHART_4.10.00_AOI.L5X` *(Trang 76 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Mở rộng từ `P_AIn` để đọc đồng thời tín hiệu 4–20mA và gói dữ liệu số **HART** từ thẻ Analog Input có tích hợp modem HART: đọc 4 biến động (PV, SV, TV, QV) và giải mã bit trạng thái chẩn đoán thiết bị đo hiện trường.
- **Các nhu cầu thực tế CẦN dùng khối `P_AInHART`:** Khi sử dụng đồng hồ lưu lượng Coriolis/Vortex hoặc cảm biến thông minh có giao thức HART và muốn lấy đồng thời cả Lưu lượng khối lượng (PV), Khối lượng riêng (SV) và Nhiệt độ (TV) trên cùng 1 đôi dây 4–20mA.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi thẻ I/O hoặc cảm biến không hỗ trợ HART (dùng [`P_AIn.md`](./P_AIn.md)).

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
