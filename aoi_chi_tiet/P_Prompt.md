# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Prompt`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 389–390)  
**Tệp định nghĩa khối lệnh:** `P_Prompt_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Prompt`:** **12** Biến đầu vào (`Input Parameters`) | **18** Biến đầu ra (`Output Parameters`) | **0** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Prompt` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Hộp thoại Nhắc lệnh & Tương tác Người vận hành (`Operator Prompt`)**. Hiển thị thông báo trên HMI yêu cầu người vận hành xác nhận thao tác thủ công, chọn phương án từ danh sách hoặc nhập thông số thực tế trả về cho bộ điều khiển tuần tự `P_Seq`.
- **Khi nào CẦN sử dụng:** Khi kết hợp với khối **`P_Seq`**: ví dụ đến bước 5 của quy trình pha chế, hệ thống tạm dừng và bật hộp thoại `P_Prompt` nhắc công nhân *"Hãy đổ 5 kg phụ gia X vào phễu và nhấn Xác nhận (Confirm) để tiếp tục chạy bước 6"*.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Không dùng `P_Prompt` để báo động sự cố (sự cố bất thường phải dùng `P_Alarm`).

### Quy trình 4 bước đấu nối và cấu hình `P_Prompt`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Req`, `Inp_Ref`, `Inp_ScalePct`, `Inp_Real_0`, `Inp_Real_1`, `Inp_Real_2`, `Inp_Real_3`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasMoreObj`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** Không có biến `Cfg_*` cục bộ
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Prompt` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Prompt` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_Prompt` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Cfm`, `Out_Rdy`, `Out_Select`, `Out_Real_0`, `Out_Real_1`, `Out_Real_2`, `Out_Real_3`, `Val_Ref`, `Sts_Alert`, `Sts_RefChg`, `Sts_Err`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 12 Biến Đầu Vào (`Input Parameters`) của `P_Prompt`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Inp_Req`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt yêu cầu bật hộp thoại nhắc lệnh Người vận hành (1 = Enable Operator Prompt) |
| **`Inp_Ref`** | `DINT` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Số thứ tự cấu hình thông báo nhắc lệnh trong mảng Prompts cần gọi hiển thị |
| **`Inp_ScalePct`** | `REAL` | `100` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Hệ số phần trăm (%) dùng để nhân tỷ lệ các giá trị thực xuất ra hoặc hiển thị trong trình tự P_Seq |
| **`Inp_Real_0`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị số thực (#0) từ chương trình PLC truyền vào để hiển thị trên hộp thoại nhắc lệnh P_Prompt |
| **`Inp_Real_1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị số thực (#1) từ chương trình PLC truyền vào để hiển thị trên hộp thoại nhắc lệnh P_Prompt |
| **`Inp_Real_2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị số thực (#2) từ chương trình PLC truyền vào để hiển thị trên hộp thoại nhắc lệnh P_Prompt |
| **`Inp_Real_3`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị số thực (#3) từ chương trình PLC truyền vào để hiển thị trên hộp thoại nhắc lệnh P_Prompt |
| **`OCmd_Cfm`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xác nhận thông báo nhắc lệnh của Người vận hành (Operator Prompt Confirmation) |
| **`Prompts`** | `P_PromptCfg[1]` | `-` | Ẩn (Tag) | Mảng cấu trúc dữ liệu (P_PromptCfg) lưu nội dung câu nhắc và các phương án trả lời của khối P_Prompt |
| **`RespData`** | `P_PromptResp` | `-` | Ẩn (Tag) | Cấu trúc dữ liệu (P_PromptResp) lưu câu trả lời gần nhất của Người vận hành dưới dạng chuỗi ký tự |

## 3. Bảng chi tiết 18 Biến Đầu Ra (`Output Parameters`) của `P_Prompt`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_Cfm`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Xung ngõ ra báo Người vận hành đã nhấn Xác nhận hộp thoại nhắc lệnh (Operator Confirmation Output) |
| **`Out_Rdy`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ báo thông báo nhắc lệnh đã sẵn sàng hiển thị trên màn hình HMI (1 = Sẵn sàng) |
| **`Out_Select`** | `SINT` | `4` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị số nguyên phản hồi phương án lựa chọn của Người vận hành trên hộp thoại nhắc lệnh |
| **`Out_Real_0`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị số thực (REAL #0) do Người vận hành nhập vào từ hộp thoại nhắc lệnh P_Prompt |
| **`Out_Real_1`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị số thực (REAL #1) do Người vận hành nhập vào từ hộp thoại nhắc lệnh P_Prompt |
| **`Out_Real_2`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị số thực (REAL #2) do Người vận hành nhập vào từ hộp thoại nhắc lệnh P_Prompt |
| **`Out_Real_3`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị số thực (REAL #3) do Người vận hành nhập vào từ hộp thoại nhắc lệnh P_Prompt |
| **`Val_Ref`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số thứ tự cấu hình thông báo nhắc lệnh (Prompt Reference) hiện đang được sử dụng |
| **`Sts_Alert`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái cảnh báo chẩn đoán dùng để hiển thị biểu tượng cảnh báo phụ trên HMI |
| **`Sts_RefChg`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Phát hiện chỉ số thông báo nhắc lệnh (Inp_Ref) vừa thay đổi trong chu kỳ quét hiện tại (xung 1 chu kỳ) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_PromptCfgSize`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Kích thước mảng trong kiểu dữ liệu UDT P_PromptCfg không khớp tiêu chuẩn |
| **`Err_RespDataSize`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 2 = Lỗi cấu hình: Kích thước mảng dữ liệu phản hồi trong kiểu UDT P_PromptResp không hợp lệ |
| **`Err_MsgRef`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Chỉ số thông báo nhắc lệnh (Inp_Ref) vượt quá kích thước mảng cấu hình Prompt |
| **`ORdy_Cfm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Cfm (Xác nhận thông báo nhắc lệnh của Người vận hành (Operator Prompt Confirmation)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Prompts`** | `P_PromptCfg[1]` | `-` | Ẩn (Tag) | Mảng cấu trúc dữ liệu (P_PromptCfg) lưu nội dung câu nhắc và các phương án trả lời của khối P_Prompt |
| **`RespData`** | `P_PromptResp` | `-` | Ẩn (Tag) | Cấu trúc dữ liệu (P_PromptResp) lưu câu trả lời gần nhất của Người vận hành dưới dạng chuỗi ký tự |
