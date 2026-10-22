# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_HiLoSel`
**Thuộc chương:** Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_HiLoSel`:** **46** Biến đầu vào (`Input Parameters`) | **21** Biến đầu ra (`Output Parameters`) | **17** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_HiLoSel`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_HiLoSel_4.10.00_AOI.L5X` *(Trang 89–91 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Chọn giá trị Cao nhất hoặc Thấp nhất (High/Low Selector)** từ tối đa **6 tín hiệu đầu vào CV** (`Inp_CV1..6`) để xuất ra 1 cơ cấu chấp hành chung (`Out_CV`), đồng thời xuất tín hiệu bám (`Out_CVxTrack`) trả ngược về các bộ PID không được chọn để chống bão hòa tích phân (Anti-Reset Windup).
- **Các nhu cầu thực tế CẦN dùng khối `P_HiLoSel`:** **Bài toán điều khiển giành quyền bảo vệ (Override / Constraint Control):** Ví dụ 1 bơm biến tần bình thường chạy theo PID lưu lượng, nhưng có thêm PID bảo vệ áp suất đầu đẩy không quá cao và PID bảo vệ áp suất đầu hút không quá thấp. `P_HiLoSel` (chế độ Low Select) sẽ tự động chọn đầu ra nhỏ nhất để bảo vệ đường ống và bơm.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi muốn chọn Min/Max của các **cảm biến đầu vào PV** (trước khi vào PID) → Dùng [`P_AInDual.md`](./P_AInDual.md) hoặc [`P_AInMulti.md`](./P_AInMulti.md).

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_HiLoSel`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_1`, `Inp_E1`, `Inp_Kp1`, `Inp_2`, `Inp_E2`, `Inp_Kp2`, `Inp_3`, `Inp_E3`, `Inp_Kp3`, `Inp_4`, `Inp_E4`, `Inp_Kp4`, `Inp_5`, `Inp_E5`, `Inp_Kp5`, `Inp_6`, `Inp_E6`, `Inp_Kp6`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HiLoSel`, `Cfg_HasInp1`, `Cfg_UseInp1`, `Cfg_Inp1Offset`, `Cfg_HasInp2`, `Cfg_UseInp2`, `Cfg_Inp2Offset`, `Cfg_HasInp3`, `Cfg_UseInp3`, `Cfg_Inp3Offset`, `Cfg_HasInp4`, `Cfg_UseInp4`, `Cfg_Inp4Offset`, `Cfg_HasInp5`, `Cfg_UseInp5`, `Cfg_Inp5Offset`, `Cfg_HasInp6`, `Cfg_UseInp6`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Inp1_Label`, `Cfg_Inp2_Label`, `Cfg_Inp3_Label`, `Cfg_Inp4_Label`, `Cfg_Inp5_Label`, `Cfg_Inp6_Label`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_OutNavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_HiLoSel` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out`, `Out_Inp1Prev`, `Out_UseInp1Prev`, `Out_Inp2Prev`, `Out_UseInp2Prev`, `Out_Inp3Prev`, `Out_UseInp3Prev`, `Out_Inp4Prev`, `Out_UseInp4Prev`, `Out_Inp5Prev`, `Out_UseInp5Prev`, `Out_Inp6Prev`, `Out_UseInp6Prev`, `Val_Out`, `Val_Selected`, `Sts_MaintByp`, `Sts_Err`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 46 Biến Đầu Vào (`Input Parameters`) của `P_HiLoSel`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input #1 |
| **`Inp_E1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Loop Error from Primary #1 (opt., for offset calc) |
| **`Inp_Kp1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Prop. Gain from Primary #1 (opt. for offset calc) |
| **`Inp_2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input #2 |
| **`Inp_E2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Loop Error from Primary #2 (opt., for offset calc) |
| **`Inp_Kp2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Prop. Gain from Primary #2 (opt. for offset calc) |
| **`Inp_3`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input #3 |
| **`Inp_E3`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Loop Error from Primary #3 (opt., for offset calc) |
| **`Inp_Kp3`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Prop. Gain from Primary #3 (opt. for offset calc) |
| **`Inp_4`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input #4 |
| **`Inp_E4`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Loop Error from Primary #4 (opt., for offset calc) |
| **`Inp_Kp4`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Prop. Gain from Primary #4 (opt. for offset calc) |
| **`Inp_5`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input #5 |
| **`Inp_E5`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Loop Error from Primary #5 (opt., for offset calc) |
| **`Inp_Kp5`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Prop. Gain from Primary #5 (opt. for offset calc) |
| **`Inp_6`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input #6 |
| **`Inp_E6`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Loop Error from Primary #6 (opt., for offset calc) |
| **`Inp_Kp6`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Prop. Gain from Primary #6 (opt. for offset calc) |
| **`Inp_InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization Value from downstream block |
| **`Inp_InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization Value from downstream block (1 = init to Inp_InitVal) |
| **`Cfg_HiLoSel`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Selection: 1=High-Select, 0=Low-Select |
| **`Cfg_HasInp1`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_1 is connected |
| **`Cfg_UseInp1`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_1 is included in selection (for Maintenance use) |
| **`Cfg_Inp1Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use Inp1Init = Out +/- Kp1*E1 Offset, 0 = no Offset |
| **`Cfg_HasInp2`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_2 is connected |
| **`Cfg_UseInp2`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_2 is included in selection (for Maintenance use) |
| **`Cfg_Inp2Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use Inp2Init = Out +/- Kp2*E2 Offset, 0 = no Offset |
| **`Cfg_HasInp3`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_3 is connected |
| **`Cfg_UseInp3`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_3 is included in selection (for Maintenance use) |
| **`Cfg_Inp3Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use Inp3Init = Out +/- Kp3*E3 Offset, 0 = no Offset |
| **`Cfg_HasInp4`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_4 is connected |
| **`Cfg_UseInp4`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_4 is included in selection (for Maintenance use) |
| **`Cfg_Inp4Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use Inp4Init = Out +/- Kp4*E4 Offset, 0 = no Offset |
| **`Cfg_HasInp5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_5 is connected |
| **`Cfg_UseInp5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_5 is included in selection (for Maintenance use) |
| **`Cfg_Inp5Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use Inp5Init = Out +/- Kp5*E5 Offset, 0 = no Offset |
| **`Cfg_HasInp6`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_6 is connected |
| **`Cfg_UseInp6`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Inp_6 is included in selection (for Maintenance use) |
| **`Cfg_Inp6Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use Inp6Init = Out +/- Kp6*E6 Offset, 0 = no Offset |
| **`Cfg_DecPlcs`** | `SINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị trên màn hình HMI (0..6) |
| **`Cfg_HasOutNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI to enable navigation to a connected output Output object |
| **`Cfg_HasNav`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set bits indicate which Navigation buttons are enabled .0=Inp1, ..., .5=Inp6 |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_OutMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum allowed output (clamp) |
| **`Cfg_OutMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum allowed output (clamp) |

## 3. Bảng chi tiết 21 Biến Đầu Ra (`Output Parameters`) của `P_HiLoSel`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Output - System Defined Parameter |
| **`Out`** | `REAL` | `0` | Hiện (Pin) | Output (selected min or max) for downstream block |
| **`Out_Inp1Prev`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Previous (Feedback) Input Value for Primary #1 |
| **`Out_UseInp1Prev`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Request for Primary #1 to use feedback Out_Inp1Prev |
| **`Out_Inp2Prev`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Previous (Feedback) Input Value for Primary #2 |
| **`Out_UseInp2Prev`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Request for Primary #2 to use feedback Out_Inp2Prev |
| **`Out_Inp3Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Previous (Feedback) Input Value for Primary #3 |
| **`Out_UseInp3Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Request for Primary #3 to use feedback Out_Inp3Prev |
| **`Out_Inp4Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Previous (Feedback) Input Value for Primary #4 |
| **`Out_UseInp4Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Request for Primary #4 to use feedback Out_Inp4Prev |
| **`Out_Inp5Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Previous (Feedback) Input Value for Primary #5 |
| **`Out_UseInp5Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Request for Primary #5 to use feedback Out_Inp5Prev |
| **`Out_Inp6Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Previous (Feedback) Input Value for Primary #6 |
| **`Out_UseInp6Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Request for Primary #6 to use feedback Out_Inp6Prev |
| **`Val_Out`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Output (selected min or max Input) for HMI |
| **`Val_Selected`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Selected Inp 0=Min, 1=Inp1, 2=Inp2, 3=Inp3, 4=Inp4, 5=Inp5, 6=Inp6, 7=Max, 8=Inp_InitVal |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Error in Configuration, check Err_ bits for reason |
| **`Err_Has`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Config Error: must HAVE at least one Input |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Config Error: Output Max less than or equal to Output Min, must be greater |
| **`P_HiLoSel`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 17 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_HiLoSel`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'High or Low Selector'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for display on HMI |
| **`Cfg_Inp1_Label`** | `STRING_20` | `'Input 1'` | [Biến cấu hình thông số] Input 1 Label for display on HMI |
| **`Cfg_Inp2_Label`** | `STRING_20` | `'Input 2'` | [Biến cấu hình thông số] Input 2 Label for display on HMI |
| **`Cfg_Inp3_Label`** | `STRING_20` | `'Input 3'` | [Biến cấu hình thông số] Input 3 Label for display on HMI |
| **`Cfg_Inp4_Label`** | `STRING_20` | `'Input 4'` | [Biến cấu hình thông số] Input 4 Label for display on HMI |
| **`Cfg_Inp5_Label`** | `STRING_20` | `'Input 5'` | [Biến cấu hình thông số] Input 5 Label for display on HMI |
| **`Cfg_Inp6_Label`** | `STRING_20` | `'Input 6'` | [Biến cấu hình thông số] Inpput 6 Label for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'High/Low Selector'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_NavTag`** | `STRING_NavTag[6]` | `{...}` | [Biến cấu hình thông số] Tagnames for destinations of Navigation buttons ([0]=input 1, ..., [5]=input 6) |
| **`Cfg_OutNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tagname for destination of output Navigation button |
| **`Cfg_Tag`** | `STRING_20` | `'P_HiLoSel'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`HMI_HasMore`** | `BOOL` | `0` | [Biến nội bộ giao diện HMI Faceplate] Tagname for display on HMI |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_HiLoSel'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
