# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AInDual`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_AInDual`:** **140** Biến đầu vào (`Input Parameters`) | **100** Biến đầu ra (`Output Parameters`) | **0** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_AInDual`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_AInDual_4.10.00_AOI.L5X` *(Trang 35–38 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối xử lý **cặp cảm biến kép (Dual Sensor)** cùng đo 1 đại lượng quá trình (`Inp_PVA` và `Inp_PVB`). Quy đổi và lọc riêng từng kênh, cho phép chọn đầu ra là Kênh A, Kênh B, Trung bình (`Average`), Nhỏ nhất (`Min`) hoặc Lớn nhất (`Max`), tự động loại kênh hỏng và báo động khi 2 cảm biến lệch nhau quá ngưỡng `Cfg_DiffLim`.
- **Các nhu cầu thực tế CẦN dùng khối `P_AInDual`:** Khi thiết kế điểm đo quan trọng có **2 cảm biến dự phòng nóng (1oo2 / Redundant Sensors)** để nếu 1 cảm biến đứt dây thì vòng điều khiển PID tự động chuyển sang dùng cảm biến còn lại mà không bị gián đoạn, đồng thời phát hiện sớm cảm biến bị trôi (Drift).
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi chỉ có 1 cảm biến ([`P_AIn.md`](./P_AIn.md)) hoặc có từ 3 đến 8 cảm biến ([`P_AInMulti.md`](./P_AInMulti.md)).

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
