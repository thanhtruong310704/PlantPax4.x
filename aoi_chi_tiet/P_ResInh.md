# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ResInh`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 273–278)  
**Tệp định nghĩa khối lệnh:** `P_ResInh_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_ResInh`:** **13** Biến đầu vào (`Input Parameters`) | **8** Biến đầu ra (`Output Parameters`) | **7** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_ResInh` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Khóa cấm khởi động lại (Restart Inhibit)** bảo vệ nhiệt cho động cơ công suất lớn. Tính toán mô hình tích lũy nhiệt mỗi lần khởi động: giới hạn số lần khởi động liên tiếp khi nguội/nóng, ép thời gian nghỉ tối thiểu giữa 2 lần chạy và xuất đồng hồ đếm ngược thời gian chờ cho phép chạy lại (`Out_OKToStart`).
- **Khi nào CẦN sử dụng:** Gắn kèm với **các động cơ trung thế hoặc hạ thế công suất lớn (> 75 kW)** có quán tính tải nặng (quạt lớn, máy nghiền, bơm lớn) để ngăn người vận hành nhấn Start liên tục làm cháy cuộn dây stator.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Không cần dùng cho các động cơ công suất nhỏ khởi động nhẹ nhàng.

### Quy trình 4 bước đấu nối và cấu hình `P_ResInh`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Stopped`, `Inp_Starting`, `Inp_Running`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_ThreeColdStarts`, `Cfg_FirstFailCold`, `Cfg_SubseqFailCold`, `Cfg_FirstFailHot`, `Cfg_SubseqFailHot`, `Cfg_HasMoreObj`, `Cfg_HotRestartOK`, `Cfg_RestartHot`, `Cfg_HotToCold`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_ResInh` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_ResInh` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_ResInh` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val_MinToReady`, `Val_SecToReady`, `Val_Fdbk`, `Val_State`, `Sts_Ready`, `Sts_Err`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 13 Biến Đầu Vào (`Input Parameters`) của `P_ResInh`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Stopped`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái xác nhận thiết bị đã dừng hoàn toàn (1 = Confirmed Stopped) |
| **`Inp_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái báo thiết bị đang trong quá trình khởi động (1 = Starting) |
| **`Inp_Running`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái xác nhận biến tần/thiết bị đang chạy (1 = Confirmed Running) |
| **`Cfg_ThreeColdStarts`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Khung thời gian (giờ) mà trong đó động cơ đang nguội được phép khởi động tối đa 3 lần liên tiếp |
| **`Cfg_FirstFailCold`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian động cơ đang nguội phải chờ sau lần khởi động thất bại đầu tiên trước khi cho phép đề lại (giờ) |
| **`Cfg_SubseqFailCold`** | `REAL` | `4` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian động cơ đang nguội phải chờ sau lần khởi động thất bại thứ 2 trở đi (giờ) |
| **`Cfg_FirstFailHot`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian động cơ đang nóng phải chờ sau lần khởi động thất bại đầu tiên trước khi cho phép đề lại (giờ) |
| **`Cfg_SubseqFailHot`** | `REAL` | `4` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian động cơ đang nóng phải chờ sau lần khởi động thất bại thứ 2 trở đi (giờ) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HotRestartOK`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian động cơ đã chạy liên tục đủ nóng ổn định để cho phép khởi động lại ngay sau khi dừng (giờ) |
| **`Cfg_RestartHot`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ bắt buộc để khởi động lại động cơ đang nóng nếu dừng trước khi đạt thời gian Cfg_HotRestartOK (giờ) |
| **`Cfg_HotToCold`** | `REAL` | `16` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian động cơ đang dừng cần nghỉ để nguội hoàn toàn về trạng thái Nguội (Cold Motor, tính bằng giờ) |

## 3. Bảng chi tiết 8 Biến Đầu Ra (`Output Parameters`) của `P_ResInh`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val_MinToReady`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số phút còn lại phải chờ trước khi động cơ được phép khởi động lại (hiển thị mmm:ss trên HMI) |
| **`Val_SecToReady`** | `SINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giây lẻ còn lại phải chờ trước khi động cơ được phép khởi động lại (hiển thị mmm:ss trên HMI) |
| **`Val_Fdbk`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái phản hồi thực tế từ thiết bị hiện trường (Device Feedback enum: 0 = Đang dừng/Đang chuyển trạng thái, 1 = Đang chạy/Đóng/Off, 2 = Mở/On, 3 = Phản hồi không hợp lệ) |
| **`Val_State`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái máy trạng thái nội bộ (Internal Logic State) dùng để tạo hiệu ứng chuyển động sơ đồ trạng thái trên HMI Faceplate |
| **`Sts_Ready`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị đã sẵn sàng cho phép khởi động (0 = Chưa sẵn sàng / Đang bị khóa cấm khởi động lại) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`P_ResInh`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_ResInh) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 7 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ResInh`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Restart Inhibit'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Restart Inhibit'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_ResInh'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ResInh'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
