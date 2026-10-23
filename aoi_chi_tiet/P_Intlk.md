# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Intlk`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Intlk`:** **11** Biến đầu vào (`Input Parameters`) | **8** Biến đầu ra (`Output Parameters`) | **13** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại `README.md` (Thư mục gốc)](../README.md) | [⬅ Danh mục Tra cứu Nhanh (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_Intlk`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_Intlk_4.10.00_AOI.L5X` *(Trang 340–345 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Khóa Liên động An toàn 16 đầu vào có Bắt lỗi đầu tiên (First Out) và Bypass bảo trì**. Giám sát 16 điều kiện `Inp_Intlk00..15` (cấu hình được mức 0 hoặc 1 là OK, cấu hình tự giữ Latch và quyền phép Bypass cho từng kênh). Khi vi phạm sẽ cắt thiết bị đang chạy ngay lập tức và chỉ rõ nguyên nhân đầu tiên gây dừng máy (`Val_FirstOut`).
- **Các nhu cầu thực tế CẦN dùng khối `P_Intlk`:** Gắn kèm với **mọi Động cơ, Biến tần, Van, Bộ PID** để bảo vệ thiết bị và công nghệ (ví dụ: dừng bơm ngay khi mức bồn hút quá thấp `LoLo`, áp suất đẩy quá cao `HiHi`, hoặc nhiệt độ ổ bi quá cao). *(Đặt tên thẻ: `[Tên_Thiết_Bị]_Intlk`)*.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi điều kiện chỉ cần kiểm tra lúc bắt đầu khởi động mà không cần dừng máy khi đang chạy → Dùng [`P_Perm.md`](./P_Perm.md).

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Intlk`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Intlk00…Inp_Intlk15`, `Inp_BypActive`, `Inp_LatchDefeat`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasMoreObj`, `Cfg_OKState`, `Cfg_Latched`, `Cfg_Bypassable`, `Cfg_HasNav`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_CondTxt`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Intlk` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Sts_IntlkOK`, `Sts_NBIntlkOK`, `Sts_BypActive`, `Sts_RdyReset`, `Sts_Intlk`, `Sts_FirstOut`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 11 Biến Đầu Vào (`Input Parameters`) của `P_Intlk`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Intlk00…Inp_Intlk15`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Inp_BypActive`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlock Bypassing is active. |
| **`Inp_LatchDefeat`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Latch Defeat 1 = Do not latch inputs even if configured for latching. |
| **`Inp_Reset`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Input parameter that is used to programmatically reset latched interlocks. When set to 1, all latched interlocks that require a reset are reset. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_OKState`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] Bits determine the state (0 or 1) of each input that is OK to run. |
| **`Cfg_Latched`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] Set bits determine the conditions that are latched (sealed in). |
| **`Cfg_Bypassable`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] Set bits determine the conditions that can be bypassed. |
| **`Cfg_HasNav`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] Set bits determine the Navigation buttons that are enabled. |
| **`PCmd_Reset`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để reset latched interlocks (Biến xung tự động xóa về 0 sau khi thực thi). |

## 3. Bảng chi tiết 8 Biến Đầu Ra (`Output Parameters`) của `P_Intlk`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Sts_IntlkOK`** | `BOOL` | `1` | Pin/Param | [Cờ báo trạng thái logic (Status)] Overall Interlock Status: 1 = OK to run 0 = Stop |
| **`Sts_NBIntlkOK`** | `BOOL` | `1` | Pin/Param | [Cờ báo trạng thái logic (Status)] Interlock (cannot be bypassed) Status (1 = All NB interlocks OK to run). |
| **`Sts_BypActive`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1 = Interlock Bypassing Active (interlocks that can be bypassed are ignored). |
| **`Sts_RdyReset`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=A latched interlock (returned to OK) is ready to be reset |
| **`Sts_Intlk`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Individual Interlock Status (1=Stop, 0=OK) |
| **`Sts_FirstOut`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Interlock First Out Status (bit 1 is First Not-OK condition) |
| **`P_Intlk`** | `BOOL` | `0` | Pin/Param | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 13 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Intlk`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_CondTxt`** | `STRING_20[16]` | `{...}` | [Biến cấu hình thông số] Short HMI descriptions of 16 interlock conditions. |
| **`Cfg_Desc`** | `STRING_40` | `'Interlocks with First Out'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Interlocks'` | [Biến cấu hình thông số] Label for graphic symbol that is displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_NavTag`** | `STRING_NavTag[16]` | `{...}` | [Biến cấu hình thông số] Tag names for destinations of 16 navigation buttons. IMPORTANT: This function does not apply to FactoryTalk View ME Software. |
| **`Cfg_Tag`** | `STRING_20` | `'P_Intlk'` | [Biến cấu hình thông số] Tag name for display on the HMI. This string is shown in the title bar of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Intlk'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`MSet_Bypass`** | `INT` | `2#0000_0000_0000_0000` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Individual condition Maintenance Bypass toggles |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset Latched Interlocks |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_FirstOutTxt`** | `STRING_Cond` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Text Description of First-Out Condition |
