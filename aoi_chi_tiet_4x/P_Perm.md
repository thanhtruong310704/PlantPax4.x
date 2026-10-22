# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Perm`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Perm`:** **7** Biến đầu vào (`Input Parameters`) | **6** Biến đầu ra (`Output Parameters`) | **10** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_Perm`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_Perm_4.10.00_AOI.L5X` *(Trang 353–358 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Điều kiện Cho phép Khởi động 16 đầu vào (`Permissives with Bypass`)**. Kiểm tra tối đa 16 điều kiện `Inp_Perm00..15` trước khi cho phép thiết bị khởi động (hoặc mở van), có hỗ trợ Bypass bảo trì và điều hướng nhanh tới thẻ nguyên nhân trên HMI.
- **Các nhu cầu thực tế CẦN dùng khối `P_Perm`:** Khi cần kiểm tra các **điều kiện chuẩn bị trước lúc bấm Start** (ví dụ: van đầu hút đã mở, van xả đáy đã đóng, áp suất nước làm mát phớt đã đủ) nhưng khi máy đã chạy rồi thì không dùng các tín hiệu này để cắt máy. *(Đặt tên thẻ: `[Tên_Thiết_Bị]_Perm`)*.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi điều kiện bắt buộc phải duy trì suốt quá trình chạy và nếu mất phải cắt máy ngay → Dùng [`P_Intlk.md`](./P_Intlk.md).

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Perm`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Perm00…Inp_Perm15`, `Inp_BypActive`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_OKState`, `Cfg_Bypassable`, `Cfg_HasMoreObj`, `Cfg_HasNav`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_CondTxt`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Perm` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Sts_PermOK`, `Sts_NBPermOK`, `Sts_BypActive`, `Sts_Perm`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 7 Biến Đầu Vào (`Input Parameters`) của `P_Perm`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Perm00…Inp_Perm15`** | `BOOL` | `1` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Permissive Condition [00…15]. |
| **`Inp_BypActive`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] 1=Permissive Bypassing is currently active |
| **`Cfg_OKState`** | `INT` | `2#1111_1111_1111_1111` | Pin/Param | [Biến cấu hình thông số] Bits indicate which state (0 or 1) of each input is OK to start |
| **`Cfg_Bypassable`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] Set bits indicate which conditions can be bypassed |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasNav`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] Set bits indicate which Navigation buttons are enabled |

## 3. Bảng chi tiết 6 Biến Đầu Ra (`Output Parameters`) của `P_Perm`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Sts_PermOK`** | `BOOL` | `1` | Pin/Param | [Cờ báo trạng thái logic (Status)] Overall Permissive Status (1=OK to start) |
| **`Sts_NBPermOK`** | `BOOL` | `1` | Pin/Param | [Cờ báo trạng thái logic (Status)] Non-Bypassable Permissive Status (1 =all non- bypassable Permissives OK to start) |
| **`Sts_BypActive`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Permissive Bypassing is Active (ignore bypassable permissives) |
| **`Sts_Perm`** | `INT` | `2#1111_1111_1111_1111` | Pin/Param | [Cờ báo trạng thái logic (Status)] Individual Permissive Status (1=OK, 0=don't start) |
| **`P_Perm`** | `BOOL` | `0` | Pin/Param | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 10 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Perm`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_CondTxt`** | `STRING_20[16]` | `{...}` | [Biến cấu hình thông số] Short HMI description of Permissive Conditions |
| **`Cfg_Desc`** | `STRING_40` | `'Permissives'` | [Biến cấu hình thông số] Description for display on HMI. The string shows in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Permissives'` | [Biến cấu hình thông số] Label for graphic symbol that is displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_NavTag`** | `STRING_NavTag[16]` | `{...}` | [Biến cấu hình thông số] Tag names for destination of 16 navigation buttons. IMPORTANT: This function does not apply to FactoryTalk View ME Software. |
| **`Cfg_Tag`** | `STRING_20` | `'P_Perm'` | [Biến cấu hình thông số] Tagname for display on HMI. This string shows in the title bar of the home tab of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Perm'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`MSet_Bypass`** | `INT` | `2#0000_0000_0000_0000` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Individual condition Maintenance Bypass toggles |
