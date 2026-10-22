# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ValveStats`
**Thuộc chương:** Chương 4: Van điều khiển & Cơ cấu chấp hành (Valves)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Valves 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_ValveStats`:** **18** Biến đầu vào (`Input Parameters`) | **34** Biến đầu ra (`Output Parameters`) | **8** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_ValveStats`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_ValveStats_4.10.00_AOI.L5X` *(Trang 319–322 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Thống kê & Chẩn đoán Hành trình Van 2 trạng thái (2-State Valve Statistics)**. Đo thời gian thực tế của từng lần Mở và Đóng van (`Val_LastOpenT`, `Val_LastCloseT`), tính thời gian trung bình động, độ lệch chuẩn (`StdDev`), đếm tổng số lần đóng mở (`Val_StrokeCount`) và cảnh báo khi van đóng/mở chậm bất thường.
- **Các nhu cầu thực tế CẦN dùng khối `P_ValveStats`:** Gắn song song với các khối **`P_ValveSO` hoặc `P_ValveMO` quan trọng** để bảo trì dự đoán (Predictive Maintenance): phát hiện sớm hiện tượng khô dầu, mòn phớt, yếu áp khí nén hoặc đóng cặn làm van di chuyển chậm dần trước khi van bị kẹt hẳn.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Không dùng cho van tuyến tính `P_ValveC`.

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_ValveStats`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Closed`, `Inp_Opened`, `Inp_StopOther`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasMoreObj`, `Cfg_HasStopOther`, `Cfg_SlowOpenT`, `Cfg_SlowCloseT`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_StopOtherDesc`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_ValveStats` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_ClearTotTimes`, `PCmd_ClearMaxTimes`, `PCmd_ClearStrokeCounts`, `PCmd_ClearSlowCounts`, `PCmd_ClearMAvgs`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val_CurrClosedT`, `Val_LastClosedT`, `Val_TotClosedT`, `Val_MaxClosedT`, `Val_CurrOpeningT`, `Val_LastOpeningT`, `Val_TotOpeningT`, `Val_MaxOpeningT`, `Val_MAvgOpeningT`, `Val_CurrOpenedT`, `Val_LastOpenedT`, `Val_TotOpenedT`, `Val_MaxOpenedT`, `Val_CurrClosingT`, `Val_LastClosingT`, `Val_TotClosingT`, `Val_MaxClosingT`, `Val_MAvgClosingT`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 18 Biến Đầu Vào (`Input Parameters`) của `P_ValveStats`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Closed`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Valve is confirmed Closed (from valve instruction) |
| **`Inp_Opened`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Valve is confirmed Opened (from valve instruction) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Inp_StopOther`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Valve is confirmed Stopped / Other (from valve instruction) |
| **`Cfg_HasStopOther`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve has Stopped or other state(s) to be monitored |
| **`Cfg_SlowOpenT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum Time Opening (sec) before raising Sts_SlowOpen |
| **`Cfg_SlowCloseT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum Time Closing (sec) before raising Sts_SlowClose |
| **`PCmd_ClearTotTimes`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Total Time statistics |
| **`PCmd_ClearMaxTimes`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Maximum Time statistics |
| **`PCmd_ClearStrokeCounts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Stroke Count statistics |
| **`PCmd_ClearSlowCounts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Valve Slow Count statistics |
| **`PCmd_ClearMAvgs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Moving Average Stroke Times |
| **`MCmd_ClearTotTimes`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Clear Total Time statistics |
| **`MCmd_ClearMaxTimes`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Clear Maximum Time statistics |
| **`MCmd_ClearStrokeCounts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Clear Stroke Count statistics |
| **`MCmd_ClearSlowCounts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Maintenance Cmmand to Clear Valve Slow Count statistics |
| **`MCmd_ClearMAvgs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Clear Moving Average Stroke Times |

## 3. Bảng chi tiết 34 Biến Đầu Ra (`Output Parameters`) của `P_ValveStats`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Val_CurrClosedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Current time in Closed state (sec) |
| **`Val_LastClosedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in Closed state (sec) last time valve was closed |
| **`Val_TotClosedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Accumulated time in Closed state (hours) |
| **`Val_MaxClosedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum time in Closed state (hours) of any occurrence |
| **`Val_CurrOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Current time in Opening state (sec) |
| **`Val_LastOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in Opening state (sec) last time valve was opening |
| **`Val_TotOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Accumulated time in Opening state (hours) |
| **`Val_MaxOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum time in Opening state (sec) of any occurrence |
| **`Val_MAvgOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Moving average Open stroke time, last 10 complete open strokes (sec) |
| **`Val_CurrOpenedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Current time in Opened state (sec) |
| **`Val_LastOpenedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in Opened state (sec) last time valve was opened |
| **`Val_TotOpenedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Accumulated time in Opened state (hours) |
| **`Val_MaxOpenedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum time in Opened state (hours) of any occurrence |
| **`Val_CurrClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Current time in Closing state (sec) |
| **`Val_LastClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in Closing state (sec) last time valve was closing |
| **`Val_TotClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Accumulated time in Closing state (hours) |
| **`Val_MaxClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum time in Closing state (sec) of any occurrence |
| **`Val_MAvgClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Moving average Clsoe stroke time, last 10 complete close strokes (sec) |
| **`Val_CurrStopOtherT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current time in Stopped / Other state (sec) |
| **`Val_LastStopOtherT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in Stopped / Other state (sec) last time valve was stopped (or other) |
| **`Val_TotStopOtherT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Accumulated time in Stopped / Other state (hours) |
| **`Val_MaxStopOtherT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum time in Stopped / Other state (hours) of any occurrence |
| **`Val_CpltOpenC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of complete valve Open strokes (Closed to Opened) |
| **`Val_CpltCloseC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of complete valve Close strokes (Opened to Closed) |
| **`Val_IncpltOpenC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of incomplete valve Open strokes (Closed - Moving - Closed) |
| **`Val_IncpltCloseC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of incomplete valve Close strokes (Opened - Moving - Opened) |
| **`Val_StopOtherC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of valve Stopped / Other occurrences |
| **`Val_SlowOpenC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of valve Slow to Open occurrences |
| **`Val_SlowCloseC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of valve Slow to Close occurrences |
| **`Sts_SlowOpen`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Last Closed to Opened stroke exceeded configured time threshold |
| **`Sts_SlowClose`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Last Opened to Closed stroke exceeded configured time threshold |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Lỗi cài đặt thông số cấu hình (Configuration Error): invalid Slow Open Time or Slow Close Time |
| **`P_ValveStats`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 8 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ValveStats`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'HMI'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Valve Statistics (stroke times, counts)'` | [Biến cấu hình thông số] Description for display on HMI. The string shows in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Valve Statistics'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_StopOtherDesc`** | `STRING_20` | `''` | [Biến cấu hình thông số] Name for the stopped or other state for display on HMI. |
| **`Cfg_Tag`** | `STRING_20` | `'P_ValveStats'` | [Biến cấu hình thông số] Tagname for display on HMI. This string shows in the bottom right of the Home tab of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ValveStats'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
