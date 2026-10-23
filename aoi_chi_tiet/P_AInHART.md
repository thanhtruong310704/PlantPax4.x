# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AInHART`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 76)  
**Tệp định nghĩa khối lệnh:** `P_AInHART_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_AInHART`:** **107** Biến đầu vào (`Input Parameters`) | **81** Biến đầu ra (`Output Parameters`) | **54** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_AInHART` trong Studio 5000
- **Chức năng cốt lõi:** Mở rộng từ `P_AIn` để đọc đồng thời tín hiệu 4–20mA và gói dữ liệu số **HART** từ thẻ Analog Input có tích hợp modem HART: đọc 4 biến động (PV, SV, TV, QV) và giải mã bit trạng thái chẩn đoán thiết bị đo hiện trường.
- **Khi nào CẦN sử dụng:** Khi sử dụng đồng hồ lưu lượng Coriolis/Vortex hoặc cảm biến thông minh có giao thức HART và muốn lấy đồng thời cả Lưu lượng khối lượng (PV), Khối lượng riêng (SV) và Nhiệt độ (TV) trên cùng 1 đôi dây 4–20mA.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi thẻ I/O hoặc cảm biến không hỗ trợ HART (dùng [`P_AIn.md`](./P_AIn.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_AInHART`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Ref_ChanData`, `Ref_DiagTable`, `Inp_AV`, `Inp_AVSrcQ`, `Inp_AVNotify`, `Inp_AVBad`, `Inp_AVUncertain`, `Inp_Sim`, `Inp_HiHiGate`, `Inp_HiGate`, `Inp_LoGate`, `Inp_LoLoGate`, `Inp_FailGate`, `Inp_Reset`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_ChanData`, `Ref_DiagTable` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_NoSubstPV`, `Cfg_HasMoreObj`, `Cfg_HasChanObj`, `Cfg_UseChanSrcQ`, `Cfg_HasHART`, `Cfg_HasPV`, `Cfg_HasSV`, `Cfg_HasTV`, `Cfg_HasFV`, `Cfg_UseVarSts`, `Cfg_UseHARTText`, `Cfg_UseHARTScaling`, `Cfg_AutoUpdDevInfo`, `Cfg_ManUpdDevInfo`, `Cfg_PVDecPlcs`, `Cfg_SVDecPlcs`, `Cfg_TVDecPlcs`, `Cfg_FVDecPlcs`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_FVEU`, `Cfg_FVLabel`, `Cfg_Label`, `Cfg_PVEU`, `Cfg_PVLabel`, `Cfg_SVEU`, `Cfg_SVLabel`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_AInHART` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_AInHART` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_ClearCapt`, `PCmd_Reset`, `PCmd_HiHiAck`, `PCmd_HiAck`, `PCmd_LoAck`, `PCmd_LoLoAck`, `PCmd_FailAck`, `PCmd_FailSuppress`, `PCmd_FailUnsuppress`, `PCmd_HiHiSuppress`, `PCmd_HiHiUnsuppress`, `PCmd_HiSuppress`, `PCmd_HiUnsuppress`, `PCmd_LoLoSuppress`, `PCmd_LoLoUnsuppress`, `PCmd_LoSuppress`, `PCmd_LoUnsuppress`, `PCmd_HiHiUnshelve`...
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val`, `Val_InpAV`, `Val_PVMinCapt`, `Val_PVMaxCapt`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_PV`, `Val_SV`, `Val_TV`, `Val_FV`, `Val_DiagCode1`, `Val_DiagCode2`, `Val_DiagCode3`, `Val_NAMURSts`, `Val_NAMURSts1`, `Val_NAMURSts2`, `Val_NAMURSts3`, `Val_Fault`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 107 Biến Đầu Vào (`Input Parameters`) của `P_AInHART`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Ref_ChanData`** | `HART_ChanData` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut liên kết tới cấu trúc dữ liệu kênh HART của thẻ Analog Output |
| **`Ref_DiagTable`** | `P_DiagTable[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới bảng tra cứu giải mã Mã chẩn đoán HART (Diagnostic Code) sang chuỗi văn bản mô tả |
| **`Inp_AV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu giá trị tương tự (Analog Value - AV) đọc trực tiếp từ cấu trúc dữ liệu thẻ HART (đơn vị thô Raw) |
| **`Inp_AVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu AV đầu vào (nối từ khối kênh nếu có) |
| **`Inp_AVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và trạng thái xác nhận của đối tượng kênh AV liên kết |
| **`Inp_AVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi tín hiệu AV hoặc lỗi truyền thông thẻ I/O (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_AVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái giá trị AV không đáng tin cậy / không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_HiHiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Cao (High-High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_HiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Cao (High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Thấp (Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoLoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Thấp (Low-Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_FailGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa chức năng nhập giá trị thay thế (Substitute PV): 0 = Cho phép nhân viên bảo trì nhập giá trị PV thay thế; 1 = Cấm sử dụng tính năng Substitute PV |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasChanObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối chẩn đoán kênh (P_AIChan) liên kết với khối này và bật nút điều hướng mở Faceplate tương ứng |
| **`Cfg_UseChanSrcQ`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhận trực tiếp mã nguồn gốc và chất lượng (SrcQ) từ khối kênh P_AIChan nối ở đầu vào; 0 = Khối tự tính toán mã SrcQ nội bộ |
| **`Cfg_HasHART`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Thiết bị đo có sử dụng truyền thông kỹ thuật số HART; 0 = Thiết bị chỉ dùng tín hiệu tương tự 4–20mA thuần túy |
| **`Cfg_HasPV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến sơ cấp kỹ thuật số HART (PV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_HasSV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến thứ cấp kỹ thuật số HART (SV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_HasTV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến thứ ba kỹ thuật số HART (TV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_HasFV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến thứ tư kỹ thuật số HART (FV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_UseVarSts`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng trạng thái chất lượng của các biến kỹ thuật số HART để đánh giá lỗi; 0 = Bỏ qua trạng thái biến HART |
| **`Cfg_UseHARTText`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động lấy chuỗi Tag, Mô tả, Nhãn và Đơn vị kỹ thuật đọc qua HART để hiển thị HMI; 0 = Dùng chuỗi nhập thủ công |
| **`Cfg_UseHARTScaling`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động lấy dải đo Raw và EU đọc từ thiết bị qua HART để quy đổi tỷ lệ; 0 = Dùng dải đo nhập thủ công trong Cfg_* |
| **`Cfg_AutoUpdDevInfo`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động đọc cập nhật thông tin thiết bị HART định kỳ; 0 = Không tự động cập nhật |
| **`Cfg_ManUpdDevInfo`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép yêu cầu đọc cập nhật thông tin thiết bị HART thủ công từ HMI; 0 = Không cho phép |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_SVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị SV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_TVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị TV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_FVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị FV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasHiHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Cao (High-High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Cao (High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Thấp (Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Thấp (Low-Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HiHiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Cao (High-High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Cao (High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Thấp (Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoLoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Thấp (Low-Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiHiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Cao (High-High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Cao (High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Thấp (Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoLoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Thấp (Low-Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiHiSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Cao (High-High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_HiSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Cao (High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Thấp (Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoLoSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Thấp (Low-Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_InpRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min, ví dụ 4.0 mA hoặc 0) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_InpRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max, ví dụ 20.0 mA hoặc 100) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Min (PV EU Min — hỗ trợ cả quy đổi ngược nếu đặt EUMin > EUMax) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Max (PV EU Max — hỗ trợ cả quy đổi ngược nếu đặt EUMax < EUMin) |
| **`Cfg_FiltTC`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hằng số thời gian của bộ lọc thông thấp bậc 1 cho tín hiệu PV (tính bằng giây; đặt = 0.0 nếu không lọc) |
| **`Cfg_HiHiDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Cao (High-High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Cao (High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Thấp (Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoLoDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Thấp (Low-Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_FailDB`** | `REAL` | `0.41666666000000002` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng lỗi tín hiệu / hỏng kênh đo (Signal Failure) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
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
| **`Cfg_HiHiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Cao (High-High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Cao (High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Thấp (Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoLoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_FailGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_FailHiLim`** | `REAL` | `103.958336` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn trên báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range High, đơn vị thô Raw hoặc EU) |
| **`Cfg_FailLoLim`** | `REAL` | `-2.0833333000000001` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn dưới báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range Low, đơn vị thô Raw hoặc EU) |
| **`Set_SimPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập (đơn vị kỹ thuật EU) được sử dụng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimHARTPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến sơ cấp HART (PV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`Set_SimHARTSV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến thứ cấp HART (SV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`Set_SimHARTTV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến thứ ba HART (TV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`Set_SimHARTFV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến thứ tư HART (FV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`PCmd_ClearCapt`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa giá trị đỉnh Cực tiểu / Cực đại (Min/Max Excursion) đã lưu của PV |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_HiHiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Cao (High-High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Cao (High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Thấp (Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoLoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Thấp (Low-Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Cao (High-High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_HiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Cao (High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Thấp (Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Thấp (Low-Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) ngay lập tức (biến xung tự động xóa về 0) |

## 3. Bảng chi tiết 81 Biến Đầu Ra (`Output Parameters`) của `P_AInHART`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu ra chính của biến quá trình PV (đã qua quy đổi đơn vị kỹ thuật EU, lọc nhiễu và chọn giá trị thay thế Substitute PV nếu có) |
| **`Val_InpAV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tương tự thực tế tính từ kênh 4–20mA (trước khi qua bộ chọn Substitute PV) |
| **`Val_PVMinCapt`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu (Minimum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVMaxCapt`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại (Maximum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đo đơn vị kỹ thuật PV = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đo đơn vị kỹ thuật PV = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến sơ cấp kỹ thuật số (HART Primary Variable - PV, tính theo đơn vị PVEU) |
| **`Val_SV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến thứ cấp kỹ thuật số (HART Secondary Variable - SV, tính theo đơn vị SVEU) |
| **`Val_TV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến thứ ba kỹ thuật số (HART Third Variable - TV, tính theo đơn vị TVEU) |
| **`Val_FV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến thứ tư kỹ thuật số (HART Fourth Variable - FV, tính theo đơn vị FVEU) |
| **`Val_DiagCode1`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã chẩn đoán thiết bị HART số #1 (-1 = Không có lỗi chẩn đoán) |
| **`Val_DiagCode2`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã chẩn đoán thiết bị HART số #2 (-1 = Không có lỗi chẩn đoán) |
| **`Val_DiagCode3`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã chẩn đoán thiết bị HART số #3 (-1 = Không có lỗi chẩn đoán) |
| **`Val_NAMURSts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái chẩn đoán chuẩn NAMUR NE107 tổng hợp (mức xấu nhất trong 3 mã chẩn đoán HART) dùng để hiển thị biểu tượng trên HMI |
| **`Val_NAMURSts1`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phân loại trạng thái chuẩn NAMUR NE107 cho mã chẩn đoán HART #1: 0 = Tốt (OK), 1 = Thông tin (Info), 2 = Yêu cầu bảo trì (Maintenance Required), 4 = Ngoài đặc tính (Off Spec), 8 = Kiểm tra chức năng (Function Check), 16 = Lỗi hỏng (Failure) |
| **`Val_NAMURSts2`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phân loại trạng thái chuẩn NAMUR NE107 cho mã chẩn đoán HART #2: 0 = Tốt (OK), 1 = Thông tin (Info), 2 = Yêu cầu bảo trì (Maintenance Required), 4 = Ngoài đặc tính (Off Spec), 8 = Kiểm tra chức năng (Function Check), 16 = Lỗi hỏng (Failure) |
| **`Val_NAMURSts3`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phân loại trạng thái chuẩn NAMUR NE107 cho mã chẩn đoán HART #3: 0 = Tốt (OK), 1 = Thông tin (Info), 2 = Yêu cầu bảo trì (Maintenance Required), 4 = Ngoài đặc tính (Off Spec), 8 = Kiểm tra chức năng (Function Check), 16 = Lỗi hỏng (Failure) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`SrcQ_PV`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến sơ cấp HART PV (kiểu liệt kê enum) |
| **`SrcQ_SV`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến thứ cấp HART SV (kiểu liệt kê enum) |
| **`SrcQ_TV`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến thứ ba HART TV (kiểu liệt kê enum) |
| **`SrcQ_FV`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến thứ tư HART FV (kiểu liệt kê enum) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
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
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Cao (High-High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiHiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Cao (High-High) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Cao (High-High) |
| **`Alm_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Cao (High-High) |
| **`Ack_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Cao (High-High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiHiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiHiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiHiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Cao (High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Cao (High) đã đếm xong, đang cho phép giám sát |
| **`Sts_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Cao (High) |
| **`Alm_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Cao (High) |
| **`Ack_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Cao (High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Thấp (Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Thấp (Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Thấp (Low) |
| **`Alm_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Thấp (Low) |
| **`Ack_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Thấp (Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Thấp (Low-Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoLoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Thấp (Low-Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Thấp (Low-Low) |
| **`Alm_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Thấp (Low-Low) |
| **`Ack_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Thấp (Low-Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoLoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoLoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoLoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của lỗi tín hiệu / hỏng kênh đo (Signal Failure) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_FailGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã đếm xong, đang cho phép giám sát |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Ack_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`P_AInHART`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_AInHART) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 54 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AInHART`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Input (HART)'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật (Engineering Units, ví dụ: "°C", "bar", "m3/h", "%") hiển thị trên màn hình HMI |
| **`Cfg_FVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến thứ tư HART FV hiển thị trên màn hình HMI |
| **`Cfg_FVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến thứ tư (Fourth Variable - FV) hiển thị trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Input (HART)'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_PVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến sơ cấp PV hiển thị trên màn hình HMI |
| **`Cfg_PVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến sơ cấp (Primary Variable - PV) hiển thị trên HMI |
| **`Cfg_SVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến thứ cấp HART SV hiển thị trên màn hình HMI |
| **`Cfg_SVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến thứ cấp (Secondary Variable - SV) hiển thị trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AInHART'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Cfg_TVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến thứ ba HART TV hiển thị trên màn hình HMI |
| **`Cfg_TVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến thứ ba (Third Variable - TV) hiển thị trên HMI |
| **`Fail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`FailGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Hi`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Cao (High) |
| **`HiGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Cao (High) |
| **`HiHi`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Rất Cao (High-High) |
| **`HiHiGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Rất Cao (High-High) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AInHART'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`Lo`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Thấp (Low) |
| **`LoGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Thấp (Low) |
| **`LoLo`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Rất Thấp (Low-Low) |
| **`LoLoGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Rất Thấp (Low-Low) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập |
| **`MCmd_UpdDevInfo`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Yêu cầu đọc cập nhật thông tin định danh thiết bị từ mạng truyền thông |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_UpdDevInfo`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_UpdDevInfo (Yêu cầu đọc cập nhật thông tin định danh thiết bị từ mạng truyền thông), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MSet_SubstPV`** | `REAL` | `0` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Giá trị PV thay thế (Substitute PV, đơn vị EU) do nhân viên bảo trì nhập thủ công để ghi đè tín hiệu cảm biến |
| **`OCmd_ClearCapt`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa giá trị đỉnh Cực tiểu / Cực đại (Min/Max Excursion) đã lưu của PV |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_DiagDesc1`** | `STRING_32` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chẩn đoán thiết bị số #1 (tra cứu từ mã Val_DiagCode1) |
| **`Val_DiagDesc2`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chẩn đoán thiết bị số #2 (tra cứu từ mã Val_DiagCode2) |
| **`Val_DiagDesc3`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chẩn đoán thiết bị số #3 (tra cứu từ mã Val_DiagCode3) |
| **`Val_HART_Desc`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi mô tả (Description Text) đọc trực tiếp từ thiết bị hiện trường qua giao thức HART |
| **`Val_HART_EU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của giá trị tương tự đọc qua giao thức HART |
| **`Val_HART_FVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến thứ tư FV đọc qua giao thức HART |
| **`Val_HART_InpRawMax`** | `REAL` | `100` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên tín hiệu thô (Raw Max) nhận được qua giao thức HART |
| **`Val_HART_InpRawMin`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới tín hiệu thô (Raw Min) nhận được qua giao thức HART |
| **`Val_HART_Label`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi nhãn hiển thị (Label Text) đọc trực tiếp từ thiết bị hiện trường qua giao thức HART |
| **`Val_HART_PVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến sơ cấp PV đọc qua giao thức HART |
| **`Val_HART_PVEUMax`** | `REAL` | `100` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên dải đo kỹ thuật (PV EU Max) nhận được qua giao thức HART |
| **`Val_HART_PVEUMin`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới dải đo kỹ thuật (PV EU Min) nhận được qua giao thức HART |
| **`Val_HART_SVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến thứ cấp SV đọc qua giao thức HART |
| **`Val_HART_Tag`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi tên thẻ (Tag Text) đọc trực tiếp từ bộ nhớ thiết bị hiện trường qua giao thức HART |
| **`Val_HART_TVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến thứ ba TV đọc qua giao thức HART |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
