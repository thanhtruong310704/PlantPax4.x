# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_CmdSrc`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 378–388)  
**Tệp định nghĩa khối lệnh:** `P_CmdSrc_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_CmdSrc`:** **37** Biến đầu vào (`Input Parameters`) | **28** Biến đầu ra (`Output Parameters`) | **2** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_CmdSrc` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Quản lý Phân quyền Nguồn lệnh (`Command Source`)** giữa 7 chế độ: Operator, Program, External, Override, Maintenance, Hand, Out of Service.
- **Khi nào CẦN sử dụng:** Đã được nhúng sẵn bên trong mọi khối chấp hành của thư viện. Bạn chỉ dùng `P_CmdSrc` độc lập khi **tự xây dựng một khối AOI thiết bị mới của riêng mình** và muốn nó có cơ chế chuyển mạch `Oper / Prog / Maint / Hand / OoS` chuẩn PlantPAx.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Không cần gọi riêng khi dùng các khối thiết bị có sẵn của thư viện.

### Quy trình 4 bước đấu nối và cấu hình `P_CmdSrc`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Hand`, `Inp_Ovrd`, `Inp_ExtInh`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasOper`, `Cfg_HasOperLocked`, `Cfg_HasProg`, `Cfg_HasProgLocked`, `Cfg_HasExt`, `Cfg_HasMaint`, `Cfg_HasMOoS`, `Cfg_OvrdOverLock`, `Cfg_ExtOverLock`, `Cfg_ProgPwrUp`, `Cfg_ProgNormal`, `Cfg_PCmdPriority`, `Cfg_PCmdProgAsLevel`, `Cfg_PCmdLockAsLevel`, `Cfg_ExtAcqAsLevel`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** Không có biến `Cfg_*` cục bộ
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_CmdSrc` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_CmdSrc` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Oper`, `PCmd_Prog`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Sts_eSrc`, `Sts_bSrc`, `Sts_Hand`, `Sts_OoS`, `Sts_Maint`, `Sts_Ovrd`, `Sts_Ext`, `Sts_Prog`, `Sts_ProgLocked`, `Sts_Oper`, `Sts_OperLocked`, `Sts_ProgOperSel`, `Sts_ProgOperLock`, `Sts_Normal`, `Sts_ExtReqInh`, `Sts_ProgReqInh`, `Sts_MAcqRcvd`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 37 Biến Đầu Vào (`Input Parameters`) của `P_CmdSrc`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_ExtInh`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Cfg_HasOper`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng chế độ Người vận hành (Operator — không khóa) |
| **`Cfg_HasOperLocked`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng chế độ Khóa Người vận hành (Operator Locked) |
| **`Cfg_HasProg`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng chế độ Chương trình tự động (Program — không khóa) |
| **`Cfg_HasProgLocked`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng chế độ Khóa Chương trình tự động (Program Locked) |
| **`Cfg_HasExt`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng chế độ Điều khiển ngoài (External) |
| **`Cfg_HasMaint`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng chế độ Bảo trì (Maintenance) |
| **`Cfg_HasMOoS`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép nhân viên bảo trì chuyển thiết bị sang chế độ Ngưng phục vụ (Out of Service) |
| **`Cfg_OvrdOverLock`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép chế độ Override ghi đè chiếm quyền ngay cả khi đang bị khóa ở Program/Operator; 0 = Tôn trọng khóa Lock |
| **`Cfg_ExtOverLock`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép chế độ External ghi đè chiếm quyền ngay cả khi đang bị khóa ở Program/Operator; 0 = Tôn trọng khóa Lock |
| **`Cfg_ProgPwrUp`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Nguồn lệnh mặc định khi vừa bật nguồn PLC (Powerup): 1 = Khởi động vào chế độ Program; 0 = Khởi động vào chế độ Operator |
| **`Cfg_ProgNormal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Nguồn lệnh chuẩn mặc định khi nhận lệnh Normal: 1 = Chế độ Program là Normal; 0 = Chế độ Operator là Normal |
| **`Cfg_PCmdPriority`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Quyền ưu tiên khi có lệnh đồng thời: 1 = Lệnh từ Chương trình (PCmd) thắng; 0 = Lệnh từ Người vận hành (OCmd) thắng |
| **`Cfg_PCmdProgAsLevel`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chân PCmd_Prog hoạt động theo mức duy trì (Level: 1 = Program, 0 = Operator); 0 = Hoạt động theo sườn xung (Edge) |
| **`Cfg_PCmdLockAsLevel`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chân PCmd_Lock hoạt động theo mức duy trì (Level: 1 = Khóa, 0 = Mở khóa); 0 = Hoạt động theo sườn xung (Edge) |
| **`Cfg_ExtAcqAsLevel`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chân XCmd_Acq hoạt động theo mức duy trì (1 = Chiếm quyền External, 0 = Nhả quyền); 0 = Hoạt động theo sườn xung |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`MCmd_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Đưa thiết bị vào trạng thái Ngưng phục vụ (Out of Service) |
| **`MCmd_IS`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Đưa thiết bị trở lại trạng thái Phục vụ bình thường (In Service) |
| **`MCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`OCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`OCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`OCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`XRdy_Acq`** | `BOOL` | `0` | Ẩn (Tag) | 1 = Sẵn sàng nhận lệnh Chiếm quyền điều khiển từ hệ thống ngoài (XCmd_Acq) |
| **`XRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | 1 = Sẵn sàng nhận lệnh Nhả quyền điều khiển từ hệ thống ngoài (XCmd_Rel) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |

## 3. Bảng chi tiết 28 Biến Đầu Ra (`Output Parameters`) của `P_CmdSrc`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Sts_eSrc`** | `INT` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Mã liệt kê nguồn lệnh hiện hành (.8=Ext, .7=OoS, .6=Oper, .5=Prog, .4=Ovrd, .3=Maint, .2=Hand, .1=Norm, .0=Lock) |
| **`Sts_bSrc`** | `INT` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (Bitmap) biểu thị nguồn lệnh đang hoạt động (dùng để hiển thị cột trạng thái nguồn lệnh trên HMI) |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển bằng tay tại chỗ (Hand — ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Ngưng phục vụ (Out of Service — ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Bảo trì (Maintenance — ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Cưỡng bức ưu tiên (Override — ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển ngoài (External — ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động (Program) |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động và đã Khóa (Program Locked — không cho Operator lấy quyền) |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành trên HMI (Operator) |
| **`Sts_OperLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành và đã Khóa (Operator Locked — không cho Program lấy quyền) |
| **`Sts_ProgOperSel`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái chốt chọn giữa Program và Operator: 1 = Program, 0 = Operator |
| **`Sts_ProgOperLock`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái chốt khóa nguồn lệnh: 1 = Đang khóa (Locked), 0 = Không khóa (Unlocked) |
| **`Sts_Normal`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn lệnh hiện hành đang khớp với nguồn lệnh Bình thường mặc định (Program hoặc Operator theo Cfg_ProgNormal) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ External đang bị khóa cấm (không thể chuyển sang External từ trạng thái hiện tại) |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ Program đang bị khóa cấm (không thể chuyển sang Program từ trạng thái hiện tại) |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đã nhận được lệnh Chiếm quyền bảo trì (MCmd_Acq) trong chu kỳ quét hiện tại |
| **`MRdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_OoS (Đưa thiết bị vào trạng thái Ngưng phục vụ (Out of Service)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_IS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_IS (Đưa thiết bị trở lại trạng thái Phục vụ bình thường (In Service)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Acq (Chiếm quyền điều khiển (Acquire) về chế độ tương ứng), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Oper (Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Prog (Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Lock (Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Normal (Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_CmdSrc`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_CmdSrc) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 2 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_CmdSrc`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`HMI_Lib`** | `STRING_8` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Type`** | `STRING_12` | `P_CmdSrc'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
