# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_D4SD`
**Thuộc chương:** Chương 4: Van điều khiển & Cơ cấu chấp hành (Valves)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Valves 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_D4SD`:** **117** Biến đầu vào (`Input Parameters`) | **80** Biến đầu ra (`Output Parameters`) | **33** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại `README.md` (Thư mục gốc)](../README.md) | [⬅ Danh mục Tra cứu Nhanh (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_D4SD`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_D4SD_4.10.00_AOI.L5X` *(Trang 359–366 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối điều khiển **Thiết bị Rời rạc 2, 3 hoặc 4 Trạng thái (`Discrete 2-, 3-, or 4-State Device`)**. Cho phép định nghĩa bảng chân trị gồm tối đa **4 ngõ ra (`Out_0..3` — giữ nguyên hoặc phát xung)** và **4 ngõ vào phản hồi (`Inp_Fdbk0..3`)** cho từng trạng thái hoạt động.
- **Các nhu cầu thực tế CẦN dùng khối `P_D4SD`:** Khi điều khiển các **cụm thiết bị chấp hành tổ hợp đặc thù** không khớp với các khối chuẩn: ví dụ bộ van khí nén 3 vị trí (Đóng / Mở hé / Mở hết), cụm cửa gió Damper 4 chế độ, hoặc máy bơm có chế độ Chạy Thủy lực / Chạy Rửa / Dừng.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi thiết bị là Động cơ chuẩn (`P_Motor*`), Van chuẩn (`P_Valve*`) hoặc Thiết bị 2..8 vị trí xoay/tịnh tiến ([`P_nPos.md`](./P_nPos.md)).

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_D4SD`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_FdbkA`, `Inp_FdbkB`, `Inp_FdbkC`, `Inp_FdbkD`, `Inp_DeviceFault`, `Inp_PermOK0`, `Inp_NBPermOK0`, `Inp_PermOK1`, `Inp_NBPermOK1`, `Inp_PermOK2`, `Inp_NBPermOK2`, `Inp_PermOK3`, `Inp_NBPermOK3`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_St0onShed`, `Cfg_NumStates`, `Cfg_OutSt0Write`, `Cfg_OutSt0State`, `Cfg_OutSt1Write`, `Cfg_OutSt1State`, `Cfg_OutSt2Write`, `Cfg_OutSt2State`, `Cfg_OutSt3Write`, `Cfg_OutSt3State`, `Cfg_FdbkSt0Check`, `Cfg_FdbkSt0State`, `Cfg_FdbkSt1Check`, `Cfg_FdbkSt1State`, `Cfg_FdbkSt2Check`, `Cfg_FdbkSt2State`, `Cfg_FdbkSt3Check`, `Cfg_FdbkSt3State`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_St0Text`, `Cfg_St1Text`, `Cfg_St2Text`, `Cfg_St3Text`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_D4SD` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_St0`, `PCmd_St1`, `PCmd_St2`, `PCmd_St3`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailAck`, `PCmd_DeviceFaultAck`, `PCmd_IntlkTripAck`, `PCmd_IOFaultAck`, `PCmd_FailSuppress`, `PCmd_FailUnsuppress`, `PCmd_DeviceFaultSuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_A`, `Out_B`, `Out_C`, `Out_D`, `Out_Horn`, `Val_Cmd`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_St0`, `Sts_St1`, `Sts_St2`, `Sts_St3`, `Sts_Moving`, `Sts_Horn`, `Sts_Available`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 117 Biến Đầu Vào (`Input Parameters`) của `P_D4SD`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_FdbkA`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback input signals from device. Use the feedback configuration parameters (for example, Cfg_FdbkSt0Check and Cfg_FdbkSt0State) to determine how these inputs are used to determine actual state. |
| **`Inp_FdbkB`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback input signals from device. Use the feedback configuration parameters (for example, Cfg_FdbkSt0Check and Cfg_FdbkSt0State) to determine how these inputs are used to determine actual state. |
| **`Inp_FdbkC`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback input signals from device. Use the feedback configuration parameters (for example, Cfg_FdbkSt0Check and Cfg_FdbkSt0State) to determine how these inputs are used to determine actual state. |
| **`Inp_FdbkD`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback input signals from device. Use the feedback configuration parameters (for example, Cfg_FdbkSt0Check and Cfg_FdbkSt0State) to determine how these inputs are used to determine actual state. |
| **`Inp_DeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Signal: Device Fault from device |
| **`Inp_PermOK0`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, device can be commanded to State 0 |
| **`Inp_NBPermOK0`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, device can be commanded to State 0 |
| **`Inp_PermOK1`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, device can be commanded to State 1 |
| **`Inp_NBPermOK1`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, device can be commanded to State 1 |
| **`Inp_PermOK2`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, device can be commanded to State 2 |
| **`Inp_NBPermOK2`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, device can be commanded to State 2 |
| **`Inp_PermOK3`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, device can be commanded to State 3 |
| **`Inp_NBPermOK3`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, device can be commanded to State 3 |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks OK, 0=Not OK, go to State 0 |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Interlocks OK, 0=Not OK, go to State 0 |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0=OK, 1=fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction simulates a working device while keeping outputs de-energized. When set to 0, the instruction controls the device normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Hand (typ. hardwired local), 0=Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Override (higher priority program logic), 0=Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Device Command: 0=None, 1=St.0, 2=St.1, 3=St.2, 4=St.3 |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset all fault conditions and latched Alarms |
| **`Cfg_St0onShed`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Go to State 0 on Shed, 0=Hold position on Shed |
| **`Cfg_NumStates`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of device states (2..4) |
| **`Cfg_OutSt0Write`** | `SINT` | `2#0000_1111` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt0State`** | `SINT` | `2#0000_0001` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt1Write`** | `SINT` | `2#0000_1111` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt1State`** | `SINT` | `2#0000_0010` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt2Write`** | `SINT` | `2#0000_1111` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt2State`** | `SINT` | `2#0000_0100` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt3Write`** | `SINT` | `2#0000_1111` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt3State`** | `SINT` | `2#0000_1000` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_FdbkSt0Check`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt0State`** | `SINT` | `2#0000_0001` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt1Check`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt1State`** | `SINT` | `2#0000_0010` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt2Check`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt2State`** | `SINT` | `2#0000_0100` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt3Check`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt3State`** | `SINT` | `2#0000_1000` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasPerm0Obj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasPerm1Obj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasPerm2Obj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasPerm3Obj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_OperSt0Prio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=OCmd_St0 any time, 0=OCmd_St0 only when Oper selected |
| **`Cfg_ExtSt0Prio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=XCmd_St0 any time, 0=XCmd_St0 only when Ext selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New device OCmd resets shed latches and cleared alarms, 0=OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New device XCmd resets shed latches and cleared alarms, 0=XCmdReset req'd. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Override ignores Bypassable Perm/ Intlk; 0=always use Perm/Intlk |
| **`Cfg_ShedOnFail`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Go to State 0 and Alarm on Fail to reach position; 0=Alarm only on Fail |
| **`Cfg_ShedOnDeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Go to State 0 and Alarm on Device Fault; 0=Alarm only on Device Fault |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Go to State 0 and Alarm on I/O Fault; 0=Alarm only on I/O Fault |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Fail to reach state Alarm exists and will be checked |
| **`Cfg_HasDeviceFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Device Fault Alarm exists and will be checked |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Interlock Trip Alarm exists and will be checked |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm exists and will be checked |
| **`Cfg_HornOnChange`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 0 = Horn on energize only, 1 = Horn on any state change |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_DeviceFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_DeviceFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_DeviceFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_OutAPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Output A (sec) (0=output held continuously) |
| **`Cfg_OutBPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Output B (sec) (0=output held continuously) |
| **`Cfg_OutCPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Output C (sec) (0=output held continuously) |
| **`Cfg_OutDPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Output D (sec) (0=output held continuously) |
| **`Cfg_SimFdbkT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Delay to echo back reaching state when in Simulation (sec) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to sound audible on commanded start (sec) (0=disabled) |
| **`Cfg_HornOnChange`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 0 = Horn on energize only, 1 = Horn on any state change |
| **`Cfg_FailT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time after new state requested to reach that state before Fault (sec) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_St0`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set device to State 0 |
| **`PCmd_St1`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set device to State 1 |
| **`PCmd_St2`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set device to State 2 |
| **`PCmd_St3`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set device to State 3 |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để ResetShed Latches and Cleared Alarms |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_DeviceFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Reach Target Position Alarm |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Reach Target Position Alarm |
| **`PCmd_DeviceFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Device Fault Alarm |
| **`PCmd_DeviceFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Device Fault Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_DeviceFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |
| **`XCmd_St0`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set device to State 0 |
| **`XCmd_St1`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set device to State 1 |
| **`XCmd_St2`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set device to State 2 |
| **`XCmd_St3`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set device to State 3 |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |

## 3. Bảng chi tiết 80 Biến Đầu Ra (`Output Parameters`) của `P_D4SD`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_A`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output A to Device |
| **`Out_B`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output B to Device |
| **`Out_C`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output C to Device |
| **`Out_D`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output D to Device |
| **`Out_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Sound audible prior to commanded energize/ state change |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final Device state source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0=None, 1=State 0, 2=State 1, 3=State 2, 4=State 3 |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Confirmed Sts 0=Powerup/Reset, 1=State0, 2=State1, 3=State2, 4=State3, 9=Moving |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0=None, 16=PositionFail, 17=DeviceFault, 32=I/OFault, 34=CfgErr |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_St0`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device commanded to and confirmed in State 0 |
| **`Sts_St1`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device commanded to and confirmed in State 1 |
| **`Sts_St2`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device commanded to and confirmed in State 2 |
| **`Sts_St3`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device commanded to and confirmed in State 3 |
| **`Sts_Moving`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device not yet confirmed in commended state |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Horn Active |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock / Permissive Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_EqpFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: External Equipment Fault (Fault or Shed Requires Reset) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_PrioSt0`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Oper/Ext Priority State 0 Command Requires Reset |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device has a Maintenance Bypass function active |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Feeback Check timer preset (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Simulation timer preset (use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device failed to reach commended state |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Device failed to reach commended state |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Fail to reach commanded state Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to reach commanded state Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to reach commanded state Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to reach commanded state alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_DeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Fault (from Inp_DeviceFault) |
| **`Alm_DeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Device Fault Alarm |
| **`Ack_DeviceFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Device Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_DeviceFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_DeviceFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_DeviceFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device commanded to State 0 by an Interlock NOT OK (one-shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Device set to State 0 by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0=OK, 1=Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_D4SD`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 33 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_D4SD`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Discrete 2, 3 or 4 S'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Discrete Device'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_St0Text`** | `STRING_8` | `'Off'` | [Biến cấu hình thông số] Text to display in State 0 |
| **`Cfg_St1Text`** | `STRING_8` | `'On'` | [Biến cấu hình thông số] Text to display in State 1 |
| **`Cfg_St2Text`** | `STRING_8` | `''` | [Biến cấu hình thông số] Text to display in State 2 |
| **`Cfg_St3Text`** | `STRING_8` | `''` | [Biến cấu hình thông số] Text to display in State 3 |
| **`Cfg_Tag`** | `STRING_20` | `'P_D4SD'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`DeviceFault`** | `P_Alarm` | `{...}` | Device Fault from device via input |
| **`Fail`** | `P_Alarm` | `{...}` | Device Fail to reach commanded position |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_D4SD'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass, enable HMI button |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check, enable HMI button |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`OCmd_St0`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set device to State 0 |
| **`OCmd_St1`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set device to State 1 |
| **`OCmd_St2`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set device to State 2 |
| **`OCmd_St3`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set device to State 3 |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=At least one Alarm or latched Shed requires Reset |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=At least one Alarm or latched Shed requires Reset or Ack |
| **`ORdy_St0`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St0, enable HMI button |
| **`ORdy_St1`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St1, enable HMI button |
| **`ORdy_St2`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St2, enable HMI button |
| **`ORdy_St3`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St3, enable HMI button |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
