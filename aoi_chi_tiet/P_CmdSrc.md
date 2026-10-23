# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_CmdSrc`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_CmdSrc`:** **38** Biến đầu vào (`Input Parameters`) | **28** Biến đầu ra (`Output Parameters`) | **2** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại `README.md` (Thư mục gốc)](../README.md) | [⬅ Danh mục Tra cứu Nhanh (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_CmdSrc`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_CmdSrc_4.10.00_AOI.L5X` *(Trang 378–388 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Quản lý Phân quyền Nguồn lệnh (`Command Source`)** giữa 7 chế độ: Operator, Program, External, Override, Maintenance, Hand, Out of Service.
- **Các nhu cầu thực tế CẦN dùng khối `P_CmdSrc`:** Đã được nhúng sẵn bên trong mọi khối chấp hành của thư viện. Bạn chỉ dùng `P_CmdSrc` độc lập khi **tự xây dựng một khối AOI thiết bị mới của riêng mình** và muốn nó có cơ chế chuyển mạch `Oper / Prog / Maint / Hand / OoS` chuẩn PlantPAx.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Không cần gọi riêng khi dùng các khối thiết bị có sẵn của thư viện.

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_CmdSrc`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Hand`, `Inp_Ovrd`, `Inp_ExtInh`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasOper`, `Cfg_HasOperLocked`, `Cfg_HasProg`, `Cfg_HasProgLocked`, `Cfg_HasExt`, `Cfg_HasMaint`, `Cfg_HasMOoS`, `Cfg_OvrdOverLock`, `Cfg_ExtOverLock`, `Cfg_ProgPwrUp`, `Cfg_ProgNormal`, `Cfg_PCmdPriority`, `Cfg_PCmdProgAsLevel`, `Cfg_PCmdLockAsLevel`, `Cfg_ExtAcqAsLevel`, `Cfg_ExtAcqAsLevel`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_CmdSrc` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Oper`, `PCmd_Prog`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Sts_eSrc`, `Sts_bSrc`, `Sts_Hand`, `Sts_OoS`, `Sts_Maint`, `Sts_Ovrd`, `Sts_Ext`, `Sts_Prog`, `Sts_ProgLocked`, `Sts_Oper`, `Sts_OperLocked`, `Sts_ProgOperSel`, `Sts_ProgOperLock`, `Sts_Normal`, `Sts_ExtReqInh`, `Sts_ProgReqInh`, `Sts_MAcqRcvd`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 38 Biến Đầu Vào (`Input Parameters`) của `P_CmdSrc`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Hand`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Hand (typ. hardwired local), 0=Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Override (higher priority program logic), 0=Release Override |
| **`Inp_ExtInh`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Cfg_HasOper`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Operator (unlocked) exists, can be selected |
| **`Cfg_HasOperLocked`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Operator Locked exists, can be selected |
| **`Cfg_HasProg`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Program (unlocked) exists, can be selected |
| **`Cfg_HasProgLocked`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Program Locked exists, can be selected |
| **`Cfg_HasExt`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1=External exists, can be selected |
| **`Cfg_HasMaint`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Maintenance exists, can be selected |
| **`Cfg_HasMOoS`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Maintenance Out of Service exists, can be selected |
| **`Cfg_OvrdOverLock`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Override ưu tiên cao hơn Prog/Oper Lock, 0=don't override Lock |
| **`Cfg_ExtOverLock`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1=External ưu tiên cao hơn Prog/Oper Lock, 0=don't override Lock |
| **`Cfg_ProgPwrUp`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1=Power Up to Prog, 0=Power Up to Oper |
| **`Cfg_ProgNormal`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] Normal Source: 1=Program if no requests; 0=Operator if no requests |
| **`Cfg_PCmdPriority`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] Command Priority: 1=Program Cmds Win; 0=Operator Cmds Win |
| **`Cfg_PCmdProgAsLevel`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1=PCmd_Prog used as a Level (1=Prog, 0=Oper) |
| **`Cfg_PCmdLockAsLevel`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1=PCmd_Lock used as a Level (1=Lock, 0=Unlock) |
| **`Cfg_ExtAcqAsLevel`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] Decimal |
| **`PCmd_Oper`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Prog`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Lock`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`MCmd_OoS`** | `BOOL` | `0` | Pin/Param | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để select Out Of Service |
| **`MCmd_IS`** | `BOOL` | `0` | Pin/Param | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để select In Service |
| **`MCmd_Acq`** | `BOOL` | `0` | Pin/Param | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Acquire (Oper/Prog/Ovrd to Maint) |
| **`MCmd_Rel`** | `BOOL` | `0` | Pin/Param | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Oper`** | `BOOL` | `0` | Pin/Param | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để select Operator (Prog to Oper) |
| **`OCmd_Prog`** | `BOOL` | `0` | Pin/Param | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để select Program (Oper to Prog) |
| **`OCmd_Lock`** | `BOOL` | `0` | Pin/Param | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Lock Operator (disallow Program) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Pin/Param | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |
| **`OCmd_Normal`** | `BOOL` | `0` | Pin/Param | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để select Normal (Oper or Prog) |
| **`XRdy_Acq`** | `BOOL` | `0` | Pin/Param | Decimal |
| **`XRdy_Rel`** | `BOOL` | `0` | Pin/Param | Decimal |
| **`XCmd_Acq`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`Cfg_ExtAcqAsLevel`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = XCmdAcq Used as Level (1 = Acquire, 0 = Release) |

## 3. Bảng chi tiết 28 Biến Đầu Ra (`Output Parameters`) của `P_CmdSrc`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Sts_eSrc`** | `INT` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Selection enum: .8=Ext, .7=OoS, .6=Oper, .5=Prog, .4=Ovrd, .3=Maint, .2=Hand, .1=Norm, .0=Lock |
| **`Sts_bSrc`** | `INT` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Active Selection bitmap (for HMI totem pole) |
| **`Sts_Hand`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_ProgOperSel`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Prog/Oper selection (latch) state, 1=Program, 0=Operator |
| **`Sts_ProgOperLock`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Prog/Oper lock (latch) state, 1=Locked, 0=Unlocked |
| **`Sts_Normal`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Maintenance Acquire command received this scan |
| **`MRdy_OoS`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_OoS (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_IS`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_IS (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Acq`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Acq (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Rel`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Oper`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Oper (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Prog`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Prog (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Lock`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Lock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Normal`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Normal (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_CmdSrc`** | `BOOL` | `0` | Pin/Param | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 2 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_CmdSrc`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`HMI_Lib`** | `STRING_8` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Type`** | `STRING_12` | `P_CmdSrc'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
