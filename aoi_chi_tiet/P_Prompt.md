# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Prompt`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Prompt`:** **12** Biến đầu vào (`Input Parameters`) | **18** Biến đầu ra (`Output Parameters`) | **0** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại `README.md` (Thư mục gốc)](../README.md) | [⬅ Danh mục Tra cứu Nhanh (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_Prompt`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_Prompt_4.10.00_AOI.L5X` *(Trang 389–390 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Hộp thoại Nhắc lệnh & Tương tác Người vận hành (`Operator Prompt`)**. Hiển thị thông báo trên HMI yêu cầu người vận hành xác nhận thao tác thủ công, chọn phương án từ danh sách hoặc nhập thông số thực tế trả về cho bộ điều khiển tuần tự `P_Seq`.
- **Các nhu cầu thực tế CẦN dùng khối `P_Prompt`:** Khi kết hợp với khối **`P_Seq`**: ví dụ đến bước 5 của quy trình pha chế, hệ thống tạm dừng và bật hộp thoại `P_Prompt` nhắc công nhân *"Hãy đổ 5 kg phụ gia X vào phễu và nhấn Xác nhận (Confirm) để tiếp tục chạy bước 6"*.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Không dùng `P_Prompt` để báo động sự cố (sự cố bất thường phải dùng `P_Alarm`).

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Prompt`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Req`, `Inp_Ref`, `Inp_ScalePct`, `Inp_Real_0`, `Inp_Real_1`, `Inp_Real_2`, `Inp_Real_3`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasMoreObj`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Prompt` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Cfm`, `Out_Rdy`, `Out_Select`, `Out_Real_0`, `Out_Real_1`, `Out_Real_2`, `Out_Real_3`, `Val_Ref`, `Sts_Alert`, `Sts_RefChg`, `Sts_Err`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 12 Biến Đầu Vào (`Input Parameters`) của `P_Prompt`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Enable Input - System Defined Parameter |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Inp_Req`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Input to Enable an Operator Prompt |
| **`Inp_Ref`** | `DINT` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Number of Manual Prompt configurations in the array to use |
| **`Inp_ScalePct`** | `REAL` | `100` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Percent by which to scale numeric display values |
| **`Inp_Real_0`** | `REAL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Programmatic Real Value to display (0) |
| **`Inp_Real_1`** | `REAL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Programmatic Real Value to display (1) |
| **`Inp_Real_2`** | `REAL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Programmatic Real Value to display (2) |
| **`Inp_Real_3`** | `REAL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Programmatic Real Value to display (3) |
| **`OCmd_Cfm`** | `BOOL` | `0` | Pin/Param | [Lệnh từ màn hình HMI Operator] Operator Confirmation |
| **`Prompts`** | `P_PromptCfg[1]` | - | Pin/Param | Prompt instance configuration data |
| **`RespData`** | `P_PromptResp` | - | Pin/Param | structure to hold most recent response data in Character format |

## 3. Bảng chi tiết 18 Biến Đầu Ra (`Output Parameters`) của `P_Prompt`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Enable Output - System Defined Prameter |
| **`Out_Cfm`** | `BOOL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Operator Confirmation output |
| **`Out_Rdy`** | `BOOL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Prompt is ready to be displayed |
| **`Out_Select`** | `SINT` | `4` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Value of Operator response to selector |
| **`Out_Real_0`** | `REAL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Real numeric value of Operator entry (0) |
| **`Out_Real_1`** | `REAL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Real numeric value of Operator entry (1) |
| **`Out_Real_2`** | `REAL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Real numeric value of Operator entry (2) |
| **`Out_Real_3`** | `REAL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Real numeric value of Operator entry (3) |
| **`Val_Ref`** | `DINT` | `0` | Pin/Param | [Giá trị thực tế đầu ra (Logic/HMI)] Number of Manual Prompt configurations in the array used |
| **`Sts_Alert`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Status for Alert breadcrumb |
| **`Sts_RefChg`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1 = The Inp_Ref has changed (Single Scan) |
| **`Sts_Err`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1= Error in Config. See detail bits for reason |
| **`Err_PromptCfgSize`** | `BOOL` | `0` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in size of an array in the P_PromptCfg UDT definition |
| **`Err_RespDataSize`** | `BOOL` | `0` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 2 = Error in size of an array in the P_PromptResp UDT definition |
| **`Err_MsgRef`** | `BOOL` | `0` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in value on Inp_Ref (past end of prompt array) |
| **`ORdy_Cfm`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Prompt ready for Operator confirmation |
| **`Prompts`** | `P_PromptCfg[1]` | - | Pin/Param | Prompt instance configuration data |
| **`RespData`** | `P_PromptResp` | - | Pin/Param | structure to hold most recent response data in Character format |
