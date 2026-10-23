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
