# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AInAdv`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_AInAdv`:** **138** Biến đầu vào (`Input Parameters`) | **97** Biến đầu ra (`Output Parameters`) | **34** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_AInAdv`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_AInAdv_4.10.00_AOI.L5X` *(Trang 30–34 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối xử lý tín hiệu Analog nâng cao. Ngoài toàn bộ tính năng của `P_AIn`, bổ sung thêm: **Khai căn bậc hai (Square Root)**, **Ngưỡng cắt lưu lượng nhỏ (Low Flow Cutoff)**, 2 cảnh báo **Tốc độ biến thiên (`HiRoC`, `LoRoC`)** và 2 cảnh báo **Độ lệch so với tham chiếu (`HiDev`, `LoDev`)**.
- **Các nhu cầu thực tế CẦN dùng khối `P_AInAdv`:** 1. Đo lưu lượng bằng chênh áp (Orifice, Venturi, Pitot) xuất tín hiệu $\Delta P$ chưa khai căn. 2. Giám sát nhiệt độ lò phản ứng cần cảnh báo khi tốc độ tăng nhiệt quá nhanh (`HiRoC`) hoặc tụt áp đột ngột (`LoRoC`).
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi chỉ đọc cảm biến thông thường và muốn tiết kiệm bộ nhớ RAM bộ điều khiển → Dùng [`P_AIn.md`](./P_AIn.md).

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_AInAdv`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_PV`, `Inp_PVSrcQ`, `Inp_PVNotify`, `Inp_PVBad`, `Inp_PVUncertain`, `Inp_Sim`, `Inp_HiHiGate`, `Inp_HiGate`, `Inp_LoGate`, `Inp_LoLoGate`, `Inp_HiRoCGate`, `Inp_HiDevGate`, `Inp_LoDevGate`, `Inp_FailGate`, `Inp_Reset`, `Cfg_Ref`, `Cfg_HiHiLim`, `Cfg_HiLim`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_CtrlHiDB`, `Cfg_CtrlHiHiDB`, `Cfg_CtrlHiHiLim`, `Cfg_CtrlHiLim`, `Cfg_CtrlLoDB`, `Cfg_CtrlLoLim`, `Cfg_CtrlLoLoDB`, `Cfg_CtrlLoLoLim`, `Cfg_UseSqRt`, `Cfg_HasMoreObj`, `Cfg_HasRoC`, `Cfg_HasDev`, `Cfg_NoSubstPV`, `Cfg_HasChanObj`, `Cfg_UseChanSrcQ`, `Cfg_PVDecPlcs`, `Cfg_HasHiHiAlm`, `Cfg_HasHiAlm`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_AInAdv` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_ClearCapt`, `PCmd_Reset`, `PCmd_HiHiAck`, `PCmd_HiAck`, `PCmd_LoAck`, `PCmd_LoLoAck`, `PCmd_HiRoCAck`, `PCmd_HiDevAck`, `PCmd_LoDevAck`, `PCmd_FailAck`, `PCmd_HiHiSuppress`, `PCmd_HiSuppress`, `PCmd_LoSuppress`, `PCmd_LoLoSuppress`, `PCmd_FailSuppress`, `PCmd_HiHiUnsuppress`, `PCmd_HiUnsuppress`, `PCmd_LoUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val`, `Val_InpPV`, `Val_RoC`, `Val_Dev`, `Val_PVMinCapt`, `Val_PVMaxCapt`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_SubstPV`, `Sts_PVBad`, `Sts_PVUncertain`, `Sts_MaintByp`, `Sts_AlmInh`, `Sts_Err`, `Sts_RdyReset`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 138 Biến Đầu Vào (`Input Parameters`) của `P_AInAdv`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_PV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu đo lường đầu vào (Biến quá trình PV) nối từ cảm biến / thẻ Analog Input |
| **`Inp_PVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu đầu vào (nối từ chân SrcQ của khối P_AIChan nếu có). |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã trạng thái mức độ ưu tiên và xác nhận cảnh báo của đối tượng PV liên kết. |
| **`Inp_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Bad Signal Quality/Communication Status for Input (1 = Bad, 0 = OK). If PV is read from an analog input, then this is normally read from the analog log channel fault status. |
| **`Inp_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu không chắc chắn (1 = Không chắc chắn/Uncertain, 0 = Tốt/OK), dùng để xuất ra cờ Sts_PVUncertain. |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction uses simulation parameters (for example, Set_SimPV) to calculate output. When set to 0, the instruction uses input parameters (for example, Inp_PV) to calculate output. |
| **`Inp_HiHiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_HiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_LoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_LoLoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_HiRoCGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_HiDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_LoDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_FailGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân đầu vào dùng để xóa (Reset) tất cả các cảnh báo đang chờ Reset từ chương trình PLC (1 = Xóa cảnh báo). |
| **`Cfg_CtrlHiDB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High Control Deadband (EU) |
| **`Cfg_CtrlHiHiDB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High-High Control Deadband (EU) |
| **`Cfg_CtrlHiHiLim`** | `BOOL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Current High-High Control Threshold |
| **`Cfg_CtrlHiLim`** | `BOOL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Current High Control Threshold |
| **`Cfg_CtrlLoDB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Low Control Deadband (EU) |
| **`Cfg_CtrlLoLim`** | `BOOL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Current Low Control Threshold |
| **`Cfg_CtrlLoLoDB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Low-Low Control Deadband (EU) |
| **`Cfg_CtrlLoLoLim`** | `BOOL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Current Low-Low Control Threshold |
| **`Cfg_UseSqRt`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use square root scaling characterization for input from a differential pressure instrument to linearize to flow units. 0 = Use linear scaling. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasRoC`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Enables the rate of change calculation and High Rate of Change alarm. |
| **`Cfg_HasDev`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Enables reference entry and deviation calculation and alarms. |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Disallow selection of Substitute PV |
| **`Cfg_HasChanObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng channel liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_UseChanSrcQ`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Truyền thẳng mã chất lượng SrcQ từ khối kênh P_AIChan sang; 0 = Tự tính toán mã SrcQ nội bộ. |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên HMI (từ 0 đến 6 chữ số). |
| **`Cfg_HasHiHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasHiRoCAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasHiDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HiHiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoLoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiRoCResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiHiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoLoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiRoCAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiHiSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_HiSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoLoSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_HiRoCSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_HiDevSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoDevSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_InpRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min) dùng để quy đổi tỷ lệ tuyến tính. |
| **`Cfg_InpRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max) dùng để quy đổi tỷ lệ tuyến tính. |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] PV (Output) Minimum for Scaling to EU |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] PV (Output) Maximum for Scaling to EU |
| **`Cfg_FiltTC`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] PV Filter Time Constant (sec), 0.0 = unfiltered |
| **`Cfg_RateTime`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] PV Rate of Change time base (sec) 60=/min, 3600=/hr |
| **`Cfg_Ref`** | `REAL` | `0` | Hiện (Pin) | [Biến cấu hình thông số] Analog Reference for Deviation Alarms (EU) |
| **`Cfg_HiHiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current High-High Status Threshold |
| **`Cfg_HiHiDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High-High Status Deadband (EU) |
| **`Cfg_HiHiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above High-High Limit to raise Status (sec) |
| **`Cfg_HiHiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below High-High Limit (minus deadband) to clear Status (sec) |
| **`Cfg_HiHiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] High-High Status Gate Delay (s) |
| **`Cfg_HiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current High Status Threshold |
| **`Cfg_HiDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High Status Deadband (EU) |
| **`Cfg_HiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above High Limit to raise Status (sec) |
| **`Cfg_HiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below High Limit (minus deadband) to clear Status (sec) |
| **`Cfg_HiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] High Status Gate Delay (s) |
| **`Cfg_LoLim`** | `REAL` | `-1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current Low Status Threshold |
| **`Cfg_LoDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Low Status Deadband (EU) |
| **`Cfg_LoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below Low Limit to raise Status (sec) |
| **`Cfg_LoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above Low Limit (plus deadband) to clear Status (sec) |
| **`Cfg_LoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Low Status Gate Delay (s) |
| **`Cfg_LoLoLim`** | `REAL` | `-1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current Low-Low Status Threshold |
| **`Cfg_LoLoDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Low-Low Status Deadband (EU) |
| **`Cfg_LoLoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below Low-Low Limit to raise Status (sec) |
| **`Cfg_LoLoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above Low-Low Limit (plus deadband) to clear Status (sec) |
| **`Cfg_LoLoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Low-Low Status Gate Delay (s) |
| **`Cfg_HiRoCLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current High Rate of Change Status Threshold |
| **`Cfg_HiRoCDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High Rate of Change Status Deadband (EU/rate time) |
| **`Cfg_HiRoCOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above High Rate of Change Limit to raise Status (sec) |
| **`Cfg_HiRoCOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below High Rate of Change Limit (minus deadband) to clear Status (sec) |
| **`Cfg_HiRoCGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] High Rate of Change Status Gate Delay (s) |
| **`Cfg_HiDevLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current High Deviation Status Threshold |
| **`Cfg_HiDevDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High Deviation Status Deadband (EU) |
| **`Cfg_HiDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above High Deviation Limit to raise Status (sec) |
| **`Cfg_HiDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below High Deviation Limit (minus deadband) to clear Status (sec) |
| **`Cfg_HiDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] High Deviation Status Gate Delay (s) |
| **`Cfg_LoDevLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current Low Deviation Status Threshold |
| **`Cfg_LoDevDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Low Deviation Status Deadband (EU) |
| **`Cfg_LoDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below Low Deviation Limit to raise Status (sec) |
| **`Cfg_LoDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above Low Deviation Limit (plus deadband) to clear Status (sec) |
| **`Cfg_LoDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Low Deviation Status Gate Delay (s) |
| **`Cfg_FailHiLim`** | `REAL` | `103.958336` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) High Limit (EU) |
| **`Cfg_FailLoLim`** | `REAL` | `-2.0833333000000001` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) Low Limit (EU) |
| **`Cfg_FailDB`** | `REAL` | `0.41666666000000002` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) High/Low Deadband (EU) |
| **`Cfg_FailOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time Bad or Out of Range to raise Fail Status (sec) |
| **`Cfg_FailOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time OK or In Range to clear Fail Status (sec) |
| **`Cfg_FailGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Fail Status Gate Delay (s) |
| **`MSet_SubstPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ tab Bảo trì (Maintenance)] Giá trị PV thay thế do nhân viên bảo trì nhập (đơn vị kỹ thuật EU) |
| **`Set_SimPV`** | `REAL` | `0` | Ẩn (Tag) | PV used in simulation (Inp_Sim = 1) (engineering units). |
| **`PCmd_ClearCapt`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_ClearCapt = 1 để xóa giá trị cực tiểu/cực đại (Min/Max Peak) đã lưu của PV (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiHiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoLoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiRoCAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiHiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`PCmd_HiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Cao (High Alarm) |
| **`PCmd_LoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Thấp (Low Alarm) |
| **`PCmd_LoLoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Analog Input Failure Alarm |
| **`PCmd_HiHiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`PCmd_HiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Cao (High Alarm) |
| **`PCmd_LoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Thấp (Low Alarm) |
| **`PCmd_LoLoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`PCmd_HiHiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoLoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |

## 3. Bảng chi tiết 97 Biến Đầu Ra (`Output Parameters`) của `P_AInAdv`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Input Value (incl. Manual Override, if used) |
| **`Val_InpPV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào thực tế (đã quy đổi EU nhưng trước khi qua bộ chọn giá trị thay thế Substitute PV) |
| **`Val_RoC`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value Rate of Change (EU/rate time) |
| **`Val_Dev`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Calculated Deviation from Reference (EU) |
| **`Val_PVMinCapt`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV thấp nhất (Min Peak) ghi nhận được kể từ lần xóa gần nhất |
| **`Val_PVMaxCapt`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV cao nhất (Max Peak) ghi nhận được kể từ lần xóa gần nhất |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu của dải đo đơn vị kỹ thuật = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại của dải đo đơn vị kỹ thuật = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final PV Source and Quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 20 = Low 21 = High 22 = Low Deviation 23 = High Deviation 24 = Low Low 25 = High High 26 = High Rate of Change 32 = Fail 34 = Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Using Substitute PV (Input being overridden) |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu PV có chất lượng xấu (Bad Quality) hoặc vượt ngoài dải giới hạn lỗi (Out of Range) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV có chất lượng không chắc chắn (Uncertain Quality) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải thô Cfg_InpRawMin bằng Cfg_InpRawMax |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải kỹ thuật Cfg_PVEUMin bằng Cfg_PVEUMax |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: On-Delay, Off-Delay, Gate Delay Time Invalid (use 0 to 2147483 s) |
| **`Err_Rate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Rate Time (use 1.0, 60.0, 3600.0, 86400.0...) |
| **`Err_Filt`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=-Error in Config: PV filter params (RateTime, TC) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High-High kết quả so sánh ngưỡng trực tiếp 1=High-High |
| **`Sts_HiHiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High-High trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiHi`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog vượt trên ngưỡng Rất Cao (High-High) |
| **`Alm_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Rất Cao (High-High) |
| **`Ack_HiHi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã được Xác nhận (Acknowledged) |
| **`Sts_HiHiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiHiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_HiHiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_HiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High kết quả so sánh ngưỡng trực tiếp 1=High |
| **`Sts_HiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Hi`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog vượt trên ngưỡng Cao (High) |
| **`Alm_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Cao (High) |
| **`Ack_Hi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã được Xác nhận (Acknowledged) |
| **`Sts_HiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_HiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low kết quả so sánh ngưỡng trực tiếp 1=Low |
| **`Sts_LoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Lo`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog tụt dưới ngưỡng Thấp (Low) |
| **`Alm_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Thấp (Low) |
| **`Ack_Lo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã được Xác nhận (Acknowledged) |
| **`Sts_LoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low-Low kết quả so sánh ngưỡng trực tiếp 1=Low-Low |
| **`Sts_LoLoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low-Low trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_LoLo`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog tụt dưới ngưỡng Rất Thấp (Low-Low) |
| **`Alm_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Rất Thấp (Low-Low) |
| **`Ack_LoLo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã được Xác nhận (Acknowledged) |
| **`Sts_LoLoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoLoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoLoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Cảnh báo Thấp (Low Alarm) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_HiRoCCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Rate of Change kết quả so sánh ngưỡng trực tiếp 1=High |
| **`Sts_HiRoCGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Rate of Change trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiRoC`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog Input Rate of Change đang vượt trên ngưỡng Cao (High) |
| **`Alm_HiRoC`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Analog Input Rate of Change is in Cảnh báo Cao (High Alarm) |
| **`Ack_HiRoC`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=High Rate of Change Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_HiRoCDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Rate of Change Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiRoCShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Rate of Change Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_HiRoCSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Rate of Change Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_HiDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Deviation kết quả so sánh ngưỡng trực tiếp 1=Dev High |
| **`Sts_HiDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Deviation trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiDev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog Input Deviation đang vượt trên ngưỡng Cao (High) |
| **`Alm_HiDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Analog Input Deviation is in Cảnh báo Cao (High Alarm) |
| **`Ack_HiDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=High Deviation Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_HiDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Deviation Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Deviation Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_HiDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Deviation Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low Deviation kết quả so sánh ngưỡng trực tiếp 1=Dev Low |
| **`Sts_LoDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low Deviation trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_LoDev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog Input Deviation đang tụt dưới ngưỡng Thấp (Low) |
| **`Alm_LoDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Analog Input Deviation is in Cảnh báo Thấp (Low Alarm) |
| **`Ack_LoDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Low Deviation Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_LoDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Deviation Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Deviation Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Deviation Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Fail kết quả so sánh ngưỡng trực tiếp 1=Out of Range |
| **`Sts_FailGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Fail trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Fail`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ vào Analog vượt ngoài dải đo hoặc chất lượng tín hiệu xấu (PV Bad) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) (Bad or Out of Range) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_CtrlHi`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Analog Input is above High Control limit |
| **`Sts_CtrlHiHi`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Analog Input is above High-High Control limit |
| **`Sts_CtrlLo`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Analog Input is above Low Control limit |
| **`Sts_CtrlLoLo`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Analog Input is above Low-Low Control limit |
| **`P_AInAdv`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 34 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AInAdv`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Advanced Analog Input'` | [Biến cấu hình thông số] Description for display on HMI. This string shows in the title bar of the faceplate. |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Input'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AinAdv'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`Fail`** | `P_Alarm` | `{...}` | Khối con quản lý Cảnh báo lỗi ngõ vào Analog (chất lượng xấu hoặc vượt dải) |
| **`FailGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo lỗi (Fail Gate) |
| **`Hi`** | `P_Alarm` | `{...}` | Cảnh báo Cao (High Alarm) |
| **`HiDev`** | `P_Alarm` | `{...}` | High Deviation Alarm |
| **`HiDevGate`** | `P_Gate` | `{...}` | High Deviation Alarm Gate / Delay Block |
| **`HiGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Cao (Hi Gate) |
| **`HiHi`** | `P_Alarm` | `{...}` | Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`HiHiGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Rất Cao (HiHi Gate) |
| **`HiRoC`** | `P_Alarm` | `{...}` | High Rate of Change Alarm |
| **`HiRoCGate`** | `P_Gate` | `{...}` | High Rate of Change Alarm Gate / Delay Block |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AInAdv'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`Lo`** | `P_Alarm` | `{...}` | Cảnh báo Thấp (Low Alarm) |
| **`LoDev`** | `P_Alarm` | `{...}` | Low Deviation Alarm |
| **`LoDevGate`** | `P_Gate` | `{...}` | Low Deviation Alarm Gate / Delay Block |
| **`LoGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Thấp (Lo Gate) |
| **`LoLo`** | `P_Alarm` | `{...}` | Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`LoLoGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Rất Thấp (LoLo Gate) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng tín hiệu PV thực tế (chế độ bình thường) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng giá trị PV thay thế (ghi đè đầu vào) |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_ClearCapt`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Clear the captured min / max PV excursion values |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset and Xác nhận (Acknowledge) all Alarms |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của riêng khối này (enum) |
