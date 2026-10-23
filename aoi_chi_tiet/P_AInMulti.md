# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AInMulti`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 39–45)  
**Tệp định nghĩa khối lệnh:** `P_AInMulti_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_AInMulti`:** **173** Biến đầu vào (`Input Parameters`) | **110** Biến đầu ra (`Output Parameters`) | **38** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_AInMulti` trong Studio 5000
- **Chức năng cốt lõi:** Khối xử lý đa cảm biến (**từ 2 đến 8 đầu vào Analog** `Inp_PV0..7`). Hỗ trợ tính Trung bình cộng (`Average`), **Trung vị (`Median`)**, `Min`, `Max`, tự động loại bỏ cảm biến hỏng hoặc cảm biến có giá trị vọt lố bất thường (`Outlier`), và kiểm tra số lượng cảm biến tốt tối thiểu (`Cfg_MinGood`).
- **Khi nào CẦN sử dụng:** Khi đo nhiệt độ/áp suất trung bình trong buồng đốt, tháp chưng cất, hầm sấy có **3–8 cảm biến**, hoặc hệ thống an toàn biểu quyết **2oo3 (3 cảm biến lấy giá trị Trung vị Median)** giúp loại bỏ hoàn toàn nhiễu đột biến của 1 cảm biến bất kỳ.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi chỉ có 1 hoặc 2 cảm biến đơn giản.

### Quy trình 4 bước đấu nối và cấu hình `P_AInMulti`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_PVA`, `Inp_PVB`, `Inp_PVC`, `Inp_PVD`, `Inp_PVE`, `Inp_PVF`, `Inp_PVG`, `Inp_PVH`, `Inp_PVASrcQ`, `Inp_PVANotify`, `Inp_PVBSrcQ`, `Inp_PVBNotify`, `Inp_PVCSrcQ`, `Inp_PVCNotify`, `Inp_PVDSrcQ`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasMoreObj`, `Cfg_HasPVA`, `Cfg_HasPVB`, `Cfg_HasPVC`, `Cfg_HasPVD`, `Cfg_HasPVE`, `Cfg_HasPVF`, `Cfg_HasPVG`, `Cfg_HasPVH`, `Cfg_UsePVA`, `Cfg_UsePVB`, `Cfg_UsePVC`, `Cfg_UsePVD`, `Cfg_UsePVE`, `Cfg_UsePVF`, `Cfg_UsePVG`, `Cfg_UsePVH`, `Cfg_RejectUncertain`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_PVATag`, `Cfg_PVBTag`, `Cfg_PVCTag`, `Cfg_PVDTag`, `Cfg_PVETag`, `Cfg_PVFTag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_AInMulti` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_AInMulti` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_ClearCapt`, `PCmd_Reset`, `PCmd_HiHiAck`, `PCmd_HiAck`, `PCmd_LoAck`, `PCmd_LoLoAck`, `PCmd_AnyRejectAck`, `PCmd_MinGoodAck`, `PCmd_FailAck`, `PCmd_HiHiSuppress`, `PCmd_HiSuppress`, `PCmd_LoSuppress`, `PCmd_LoLoSuppress`, `PCmd_AnyRejectSuppress`, `PCmd_MinGoodSuppress`, `PCmd_FailSuppress`, `PCmd_HiHiUnsuppress`, `PCmd_HiUnsuppress`...
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val`, `Val_InpPV`, `Val_PVA`, `Val_PVB`, `Val_PVC`, `Val_PVD`, `Val_PVE`, `Val_PVF`, `Val_PVG`, `Val_PVH`, `Val_PVMinCapt`, `Val_PVMaxCapt`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_NumPVs`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 173 Biến Đầu Vào (`Input Parameters`) của `P_AInMulti`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_PVA`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh A (Input PV A) |
| **`Inp_PVB`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh B (Input PV B) |
| **`Inp_PVC`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh C (Input PV C) |
| **`Inp_PVD`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh D (Input PV D) |
| **`Inp_PVE`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh E (Input PV E) |
| **`Inp_PVF`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh F (Input PV F) |
| **`Inp_PVG`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh G (Input PV G) |
| **`Inp_PVH`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh H (Input PV H) |
| **`Inp_PVASrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến A |
| **`Inp_PVANotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến A |
| **`Inp_PVBSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến B |
| **`Inp_PVBNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến B |
| **`Inp_PVCSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến C |
| **`Inp_PVCNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến C |
| **`Inp_PVDSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến D |
| **`Inp_PVDNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến D |
| **`Inp_PVESrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến E |
| **`Inp_PVENotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến E |
| **`Inp_PVFSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến F |
| **`Inp_PVFNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến F |
| **`Inp_PVGSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến G |
| **`Inp_PVGNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến G |
| **`Inp_PVHSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến H |
| **`Inp_PVHNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến H |
| **`Inp_PVABad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến A (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVBBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến B (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVCBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến C (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVDBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến D (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVEBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến E (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVFBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến F (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVGBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến G (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVHBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến H (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVAUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến A không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVBUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến B không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVCUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến C không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVDUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến D không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVEUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến E không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVFUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến F không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVGUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến G không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVHUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến H không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_HiHiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Cao (High-High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_HiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Cao (High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Thấp (Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoLoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Thấp (Low-Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_FailGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasPVA`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến A (Inp_PVA) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến B (Inp_PVB) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVC`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến C (Inp_PVC) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVD`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến D (Inp_PVD) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVE`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến E (Inp_PVE) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVF`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến F (Inp_PVF) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVG`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến G (Inp_PVG) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVH`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến H (Inp_PVH) có tồn tại và được đấu nối vào khối |
| **`Cfg_UsePVA`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến A (Inp_PVA) vào phép tính PV tổng hợp; 0 = Tạm tách kênh A ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến B (Inp_PVB) vào phép tính PV tổng hợp; 0 = Tạm tách kênh B ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVC`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến C (Inp_PVC) vào phép tính PV tổng hợp; 0 = Tạm tách kênh C ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVD`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến D (Inp_PVD) vào phép tính PV tổng hợp; 0 = Tạm tách kênh D ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVE`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến E (Inp_PVE) vào phép tính PV tổng hợp; 0 = Tạm tách kênh E ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVF`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến F (Inp_PVF) vào phép tính PV tổng hợp; 0 = Tạm tách kênh F ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVG`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến G (Inp_PVG) vào phép tính PV tổng hợp; 0 = Tạm tách kênh G ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVH`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến H (Inp_PVH) vào phép tính PV tổng hợp; 0 = Tạm tách kênh H ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_RejectUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động loại bỏ cảm biến có chất lượng Không chắc chắn (Uncertain) ra khỏi phép tính PV tổng hợp; 0 = Vẫn chấp nhận cảm biến Uncertain |
| **`Cfg_UseStdDev`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Phương pháp phát hiện cảm biến vọt lố bất thường (Outlier Rejection): 1 = So sánh với tích của Độ lệch chuẩn (Standard Deviation × Cfg_DiffLim); 0 = So sánh trực tiếp độ lệch tuyệt đối với Cfg_DiffLim |
| **`Cfg_CalcAvg`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Phương pháp tính giá trị PV tổng hợp khi có từ 3 cảm biến tốt trở lên: 1 = Tính Trung bình cộng (Average); 0 = Lấy giá trị Trung vị (Median) |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa chức năng nhập giá trị thay thế (Substitute PV): 0 = Cho phép nhân viên bảo trì nhập giá trị PV thay thế; 1 = Cấm sử dụng tính năng Substitute PV |
| **`Cfg_HasChanObjA`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến A có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjB`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến B có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjC`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến C có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjD`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến D có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjE`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến E có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjF`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến F có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjG`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến G có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjH`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến H có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasHiHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Cao (High-High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Cao (High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Thấp (Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Thấp (Low-Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasAnyRejectAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasMinGoodAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HiHiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Cao (High-High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Cao (High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Thấp (Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoLoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Thấp (Low-Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_AnyRejectResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_MinGoodResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiHiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Cao (High-High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Cao (High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Thấp (Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoLoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Thấp (Low-Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_AnyRejectAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_MinGoodAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_MinGood`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số lượng cảm biến hoạt động tốt tối thiểu bắt buộc (từ 1 đến tổng số cảm biến Cfg_HasPV*) để giá trị PV đầu ra được công nhận là Tốt (Good) |
| **`Cfg_CalcWhen2`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Phương pháp tính giá trị PV tổng hợp khi chỉ còn đúng 2 cảm biến tốt: 0 = Trung bình cộng (Average), 1 = Nhỏ nhất (Minimum), 2 = Lớn nhất (Maximum) |
| **`Cfg_HiHiSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Cao (High-High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_HiSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Cao (High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Thấp (Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoLoSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Thấp (Low-Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_AnyRejectSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MinGoodSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_InpRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min, ví dụ 4.0 mA hoặc 0) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_InpRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max, ví dụ 20.0 mA hoặc 100) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Min (PV EU Min — hỗ trợ cả quy đổi ngược nếu đặt EUMin > EUMax) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Max (PV EU Max — hỗ trợ cả quy đổi ngược nếu đặt EUMax < EUMin) |
| **`Cfg_AbsDevLim`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng độ lệch tuyệt đối tối đa cho phép (Absolute Deviation Threshold, đơn vị EU) dùng để loại bỏ cảm biến vọt lố khi Cfg_UseStdDev = 0 |
| **`Cfg_FiltTC`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hằng số thời gian của bộ lọc thông thấp bậc 1 cho tín hiệu PV (tính bằng giây; đặt = 0.0 nếu không lọc) |
| **`Cfg_HiHiDB`** | `REAL` | `1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Cao (High-High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiDB`** | `REAL` | `1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Cao (High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoDB`** | `REAL` | `1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Thấp (Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoLoDB`** | `REAL` | `1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Thấp (Low-Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_FailDB`** | `REAL` | `0.41666666` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng lỗi tín hiệu / hỏng kênh đo (Signal Failure) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiHiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Cao (High-High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Cao (High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Thấp (Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoLoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_FailGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiHiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Rất Cao (High-High): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Cao (High): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Thấp (Low): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoLoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_FailOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiHiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Rất Cao (High-High): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Cao (High): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Thấp (Low): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoLoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_FailOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_FailHiLim`** | `REAL` | `103.958336` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn trên báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range High, đơn vị thô Raw hoặc EU) |
| **`Cfg_FailLoLim`** | `REAL` | `-2.0833333` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn dưới báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range Low, đơn vị thô Raw hoặc EU) |
| **`MSet_SubstPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Giá trị PV thay thế (Substitute PV, đơn vị EU) do nhân viên bảo trì nhập thủ công để ghi đè tín hiệu cảm biến |
| **`Set_SimPVA`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến A (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVB`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến B (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVC`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến C (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVD`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến D (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVE`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến E (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVF`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến F (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVG`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến G (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVH`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến H (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`PCmd_ClearCapt`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa giá trị đỉnh Cực tiểu / Cực đại (Min/Max Excursion) đã lưu của PV |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_HiHiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Cao (High-High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Cao (High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Thấp (Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoLoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Thấp (Low-Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_AnyRejectAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_MinGoodAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiHiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_AnyRejectSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_MinGoodSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_AnyRejectUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_MinGoodUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Cao (High-High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_HiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Cao (High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Thấp (Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Thấp (Low-Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) ngay lập tức (biến xung tự động xóa về 0) |

## 3. Bảng chi tiết 110 Biến Đầu Ra (`Output Parameters`) của `P_AInMulti`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Cfg_HiHiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Rất Cao (High-High) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_HiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Cao (High) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_LoLoLim`** | `REAL` | `-1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Rất Thấp (Low-Low) (tính theo đơn vị kỹ thuật EU) |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu ra chính của biến quá trình PV (đã qua quy đổi đơn vị kỹ thuật EU, lọc nhiễu và chọn giá trị thay thế Substitute PV nếu có) |
| **`Val_InpPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV thực tế tính từ tín hiệu đầu vào (sau khi quy đổi đơn vị EU nhưng trước khi qua bộ chọn Substitute PV) |
| **`Val_PVA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến A |
| **`Val_PVB`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến B |
| **`Val_PVC`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến C |
| **`Val_PVD`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến D |
| **`Val_PVE`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến E |
| **`Val_PVF`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến F |
| **`Val_PVG`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến G |
| **`Val_PVH`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến H |
| **`Val_PVMinCapt`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu (Minimum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVMaxCapt`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại (Maximum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đo đơn vị kỹ thuật PV = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đo đơn vị kỹ thuật PV = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_NumPVs`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số lượng kênh cảm biến đầu vào hiện đang hợp lệ và được đưa vào phép tính PV tổng hợp |
| **`SrcQ_IOA`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến A (kiểu liệt kê enum) |
| **`SrcQ_IOB`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến B (kiểu liệt kê enum) |
| **`SrcQ_IOC`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến C (kiểu liệt kê enum) |
| **`SrcQ_IOD`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến D (kiểu liệt kê enum) |
| **`SrcQ_IOE`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến E (kiểu liệt kê enum) |
| **`SrcQ_IOF`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến F (kiểu liệt kê enum) |
| **`SrcQ_IOG`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến G (kiểu liệt kê enum) |
| **`SrcQ_IOH`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến H (kiểu liệt kê enum) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_PVAReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến A đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVBReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến B đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVCReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến C đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVDReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến D đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVEReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến E đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVFReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến F đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVGReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến G đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVHReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến H đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng giá trị PV thay thế (Substitute PV do nhân viên bảo trì nhập) thay cho tín hiệu cảm biến thật |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Xấu (Bad Quality) hoặc tín hiệu vượt ngoài dải lỗi (Out of Range) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Không chắc chắn (Uncertain Quality) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải giá trị thô đầu vào (Cfg_InpRawMin = Cfg_InpRawMax) bị trùng nhau |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Filt`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số bộ lọc tín hiệu PV (Cfg_FiltTC hoặc Cfg_RateTime) không hợp lệ |
| **`Err_DB`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Độ rộng vùng chết (Deadband) của một ngưỡng cảnh báo bị đặt số âm (< 0.0) |
| **`Err_Has`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Bắt buộc phải bật ít nhất một kênh cảm biến đầu vào (Cfg_HasPVx = 1) |
| **`Err_Use`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Phải có ít nhất một kênh cảm biến được chọn đưa vào phép tính (Cfg_UsePVx = 1) |
| **`Err_MinGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Số lượng cảm biến tốt tối thiểu (Cfg_MinGood) nằm ngoài phạm vi hợp lệ [1..8] |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Cao (High-High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiHiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Cao (High-High) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Cao (High-High) |
| **`Alm_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Cao (High-High) |
| **`Ack_HiHi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Cao (High-High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiHiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiHiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiHiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Cao (High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Cao (High) đã đếm xong, đang cho phép giám sát |
| **`Sts_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Cao (High) |
| **`Alm_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Cao (High) |
| **`Ack_Hi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Cao (High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Thấp (Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Thấp (Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Thấp (Low) |
| **`Alm_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Thấp (Low) |
| **`Ack_Lo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Thấp (Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Thấp (Low-Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoLoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Thấp (Low-Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Thấp (Low-Low) |
| **`Alm_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Thấp (Low-Low) |
| **`Ack_LoLo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Thấp (Low-Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoLoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoLoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoLoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_AnyReject`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có ít nhất một kênh cảm biến đầu vào đang bị loại bỏ khỏi phép tính tổng hợp |
| **`Alm_AnyReject`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) |
| **`Ack_AnyReject`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_AnyRejectDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_AnyRejectShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_AnyRejectSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_MinGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Số lượng cảm biến tốt đã chạm mức tối thiểu bắt buộc (Cfg_MinGood); nếu mất thêm 1 cảm biến sẽ báo lỗi toàn bộ PV |
| **`Alm_MinGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) |
| **`Ack_MinGood`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_MinGoodDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_MinGoodShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_MinGoodSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của lỗi tín hiệu / hỏng kênh đo (Signal Failure) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_FailGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã đếm xong, đang cho phép giám sát |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`P_AInMulti`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_AInMulti) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 38 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AInMulti`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`AnyReject`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Multiple (to 8) Analog Input'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật (Engineering Units, ví dụ: "°C", "bar", "m3/h", "%") hiển thị trên màn hình HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Input'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_PVATag`** | `STRING_20` | `'Sensor / Input A'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh A hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVBTag`** | `STRING_20` | `'Sensor / Input B'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh B hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVCTag`** | `STRING_20` | `'Sensor / Input C'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh C hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVDTag`** | `STRING_20` | `'Sensor / Input D'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh D hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVETag`** | `STRING_20` | `'Sensor / Input E'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh E hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVFTag`** | `STRING_20` | `'Sensor / Input F'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh F hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVGTag`** | `STRING_20` | `'Sensor / Input G'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh G hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVHTag`** | `STRING_20` | `'Sensor / Input H'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh H hiển thị trên màn hình HMI Faceplate |
| **`Cfg_Tag`** | `STRING_20` | `'P_AInMulti'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Fail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`FailGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Hi`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Cao (High) |
| **`HiGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Cao (High) |
| **`HiHi`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Rất Cao (High-High) |
| **`HiHiGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Rất Cao (High-High) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AInMulti'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`Lo`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Thấp (Low) |
| **`LoGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Thấp (Low) |
| **`LoLo`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Rất Thấp (Low-Low) |
| **`LoLoGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Rất Thấp (Low-Low) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập |
| **`MinGood`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_ClearCapt`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa giá trị đỉnh Cực tiểu / Cực đại (Min/Max Excursion) đã lưu của PV |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
