# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Seq`
**Thuộc chương:** Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Seq`:** **100** Biến đầu vào (`Input Parameters`) | **96** Biến đầu ra (`Output Parameters`) | **95** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_Seq`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_Seq_4.10.00_AOI.L5X` *(Trang 96–98 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Bộ **Điều khiển Tuần tự theo Bước (Step Sequencer)** dựa trên mảng cấu trúc `P_SeqStep`. Mỗi bước điều khiển tối đa **32 bit DO** và **32 giá trị đặt REAL**, chờ tối đa **32 điều kiện phản hồi DI** kèm bộ đếm thời gian bước, hỗ trợ Rẽ nhánh (Jump), Tạm dừng (Hold), Tiếp tục (Resume) và nhắc lệnh người vận hành qua `P_Prompt`.
- **Các nhu cầu thực tế CẦN dùng khối `P_Seq`:** Khi lập trình các **quy trình chạy theo bước cố định hoặc linh hoạt** như: Chu trình rửa tự động CIP/SIP, chu trình hoàn nguyên cột lọc nước (Backwash / Regeneration), quy trình khởi động/dừng cụm máy nén hoặc pha chế mẻ nhỏ mà kỹ sư công nghệ muốn chỉnh thông số bước ngay trên HMI.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Với các nhà máy sản xuất theo mẻ lớn phức tạp chuẩn ISA-88 đa luồng song song → Dùng phần mềm chuyên dụng FactoryTalk Batch.

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
