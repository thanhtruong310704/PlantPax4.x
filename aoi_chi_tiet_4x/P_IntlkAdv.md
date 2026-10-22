# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_IntlkAdv`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_IntlkAdv`:** **15** Biến đầu vào (`Input Parameters`) | **12** Biến đầu ra (`Output Parameters`) | **15** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_IntlkAdv`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_IntlkAdv_4.10.00_AOI.L5X` *(Trang 346–352 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Khóa Liên động Nâng cao (Advanced Interlocks)**. Mở rộng từ `P_Intlk` với khả năng phân loại điều kiện liên động theo các nhóm cấp độ ưu tiên dừng (**Stop Priority / Shed Levels**) và điều hướng trạng thái an toàn linh hoạt hơn.
- **Các nhu cầu thực tế CẦN dùng khối `P_IntlkAdv`:** Khi thiết bị chấp hành có **nhiều kiểu dừng an toàn khác nhau** tùy theo mức độ sự cố (ví dụ: liên động cấp 1 thì giảm tốc dừng êm *Coast/Ramp Stop*, còn liên động khẩn cấp cấp 0 thì cắt điện phanh gấp ngay lập tức).
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi mọi điều kiện liên động đều tác động dừng máy giống nhau → Dùng [`P_Intlk.md`](./P_Intlk.md) cho gọn nhẹ.

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_IntlkAdv`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Intlk00…Inp_Intlk15`, `Inp_IOFault`, `Inp_Avail`, `Inp_BypActive`, `Inp_LatchDefeat`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasMoreObj`, `Cfg_OKState`, `Cfg_Latched`, `Cfg_Bypassable`, `Cfg_HasNav`, `Cfg_Type0…Cfg_Type15`, `Cfg_HasType`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_CondTxt`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`, `Cfg_TypeDesc`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_IntlkAdv` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Sts_IntlkOK`, `Sts_NBIntlkOK`, `Sts_BypActive`, `Sts_RdyReset`, `Sts_LatchMask`, `Sts_BypassMask`, `Sts_Intlk`, `Sts_Avail`, `Sts_FirstOut`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 15 Biến Đầu Vào (`Input Parameters`) của `P_IntlkAdv`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Intlk00…Inp_Intlk15`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Inp_IOFault`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Inp_Avail`** | `BOOL` | `2#1` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Inp_BypActive`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Inp_LatchDefeat`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Inp_Reset`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_OKState`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_Latched`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_Bypassable`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasNav`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_Type0…Cfg_Type15`** | `SINT` | `6` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasType`** | `SINT` | `2#1111_1111` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`PCmd_Reset`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |

## 3. Bảng chi tiết 12 Biến Đầu Ra (`Output Parameters`) của `P_IntlkAdv`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Biến `EnableOut` (kiểu `BOOL`) của khối lệnh. |
| **`Ref_Inputs`** | `INT` | `0` | Pin/Param | [Tham chiếu InOut nối tới cấu trúc ngoài] Biến `Ref_Inputs` (kiểu `INT`) của khối lệnh. |
| **`Sts_IntlkOK`** | `BOOL` | `1` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_IntlkOK` (kiểu `BOOL`) của khối lệnh. |
| **`Sts_NBIntlkOK`** | `BOOL` | `1` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_NBIntlkOK` (kiểu `BOOL`) của khối lệnh. |
| **`Sts_BypActive`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_BypActive` (kiểu `BOOL`) của khối lệnh. |
| **`Sts_RdyReset`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_RdyReset` (kiểu `BOOL`) của khối lệnh. |
| **`Sts_LatchMask`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_LatchMask` (kiểu `INT`) của khối lệnh. |
| **`Sts_BypassMask`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_BypassMask` (kiểu `INT`) của khối lệnh. |
| **`Sts_Intlk`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_Intlk` (kiểu `INT`) của khối lệnh. |
| **`Sts_Avail`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_Avail` (kiểu `BOOL`) của khối lệnh. |
| **`Sts_FirstOut`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_FirstOut` (kiểu `INT`) của khối lệnh. |
| **`P_Intlk`** | `BOOL` | `0` | Pin/Param | Biến `P_Intlk` (kiểu `BOOL`) của khối lệnh. |

## 4. Bảng chi tiết 15 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_IntlkAdv`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Biến `Cfg_Area` (kiểu `STRING_Area`) của khối lệnh. |
| **`Cfg_CondTxt`** | `STRING_Cond[16]` | `{...}` | [Biến cấu hình thông số] Biến `Cfg_CondTxt` (kiểu `STRING_Cond[16]`) của khối lệnh. |
| **`Cfg_Desc`** | `STRING_40` | `'Advanced Interlocks with First Out'` | [Biến cấu hình thông số] Biến `Cfg_Desc` (kiểu `STRING_40`) của khối lệnh. |
| **`Cfg_Label`** | `STRING_20` | `'Advanced Interlocks'` | [Biến cấu hình thông số] Biến `Cfg_Label` (kiểu `STRING_20`) của khối lệnh. |
| **`Cfg_NavTag`** | `STRING_NavTag[16]` | `{...}` | [Biến cấu hình thông số] Navigation Tag |
| **`Cfg_Tag`** | `STRING_20` | `'P_IntlkAdv'` | [Biến cấu hình thông số] Navigation Tag |
| **`Cfg_TypeDesc`** | `STRING_16[8]` | `{...}` | [Biến cấu hình thông số] Navigation Tag |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Navigation Tag |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Navigation Tag |
| **`HMI_Type`** | `STRING_16` | `'P_IntlkAdv'` | [Biến nội bộ giao diện HMI Faceplate] Navigation Tag |
| **`MSet_Bypass`** | `INT` | `2#0000_0000_0000_0000` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Navigation Tag |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Navigation Tag |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] Navigation Tag |
| **`Val_FirstOutTxt`** | `STRING_Cond` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Navigation Tag |
| **`Val_FirstOutType`** | `STRING_16` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Navigation Tag |
