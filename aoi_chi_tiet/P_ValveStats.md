# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ValveStats`
**Thuộc chương:** [Chương 4: Van Điều khiển & Cơ cấu Chấp hành (Valves)](../04_CHUONG_4_VALVES.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Valves 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 319–322)  
**Tệp định nghĩa khối lệnh:** `P_ValveStats_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_ValveStats`:** **18** Biến đầu vào (`Input Parameters`) | **34** Biến đầu ra (`Output Parameters`) | **8** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_ValveStats` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Thống kê & Chẩn đoán Hành trình Van 2 trạng thái (2-State Valve Statistics)**. Đo thời gian thực tế của từng lần Mở và Đóng van (`Val_LastOpenT`, `Val_LastCloseT`), tính thời gian trung bình động, độ lệch chuẩn (`StdDev`), đếm tổng số lần đóng mở (`Val_StrokeCount`) và cảnh báo khi van đóng/mở chậm bất thường.
- **Khi nào CẦN sử dụng:** Gắn song song với các khối **`P_ValveSO` hoặc `P_ValveMO` quan trọng** để bảo trì dự đoán (Predictive Maintenance): phát hiện sớm hiện tượng khô dầu, mòn phớt, yếu áp khí nén hoặc đóng cặn làm van di chuyển chậm dần trước khi van bị kẹt hẳn.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Không dùng cho van tuyến tính `P_ValveC`.

### Quy trình 4 bước đấu nối và cấu hình `P_ValveStats`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Closed`, `Inp_Opened`, `Inp_StopOther`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasMoreObj`, `Cfg_HasStopOther`, `Cfg_SlowOpenT`, `Cfg_SlowCloseT`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_StopOtherDesc`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_ValveStats` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_ValveStats` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_ClearTotTimes`, `PCmd_ClearMaxTimes`, `PCmd_ClearStrokeCounts`, `PCmd_ClearSlowCounts`, `PCmd_ClearMAvgs`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val_CurrClosedT`, `Val_LastClosedT`, `Val_TotClosedT`, `Val_MaxClosedT`, `Val_CurrOpeningT`, `Val_LastOpeningT`, `Val_TotOpeningT`, `Val_MaxOpeningT`, `Val_MAvgOpeningT`, `Val_CurrOpenedT`, `Val_LastOpenedT`, `Val_TotOpenedT`, `Val_MaxOpenedT`, `Val_CurrClosingT`, `Val_LastClosingT`, `Val_TotClosingT`, `Val_MaxClosingT`, `Val_MAvgClosingT`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 18 Biến Đầu Vào (`Input Parameters`) của `P_ValveStats`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Closed`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu trạng thái xác nhận van đã Đóng hoàn toàn (nối từ chân Sts_Closed của khối điều khiển van vào P_ValveStats) |
| **`Inp_Opened`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu trạng thái xác nhận van đã Mở hoàn toàn (nối từ chân Sts_Opened của khối điều khiển van vào P_ValveStats) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Inp_StopOther`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu trạng thái xác nhận van đang Dừng giữa chừng hoặc ở trạng thái phụ khác (nối vào P_ValveStats) |
| **`Cfg_HasStopOther`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép thống kê cả trạng thái van dừng giữa hành trình / trạng thái phụ khác trong khối P_ValveStats |
| **`Cfg_SlowOpenT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng thời gian hành trình Mở tối đa bình thường (giây): nếu van mở lâu hơn mức này sẽ bật cờ cảnh báo van mở chậm (Sts_SlowOpen) |
| **`Cfg_SlowCloseT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng thời gian hành trình Đóng tối đa bình thường (giây): nếu van đóng lâu hơn mức này sẽ bật cờ cảnh báo van đóng chậm (Sts_SlowClose) |
| **`PCmd_ClearTotTimes`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa tổng thời gian tích lũy ở các trạng thái của van về 0 |
| **`PCmd_ClearMaxTimes`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa thống kê thời gian đóng/mở/dừng cực đại của van về 0 |
| **`PCmd_ClearStrokeCounts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa bộ đếm tổng số chu kỳ hành trình đóng/mở của van về 0 |
| **`PCmd_ClearSlowCounts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa bộ đếm số lần van đóng/mở chậm vượt ngưỡng về 0 |
| **`PCmd_ClearMAvgs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa thống kê thời gian hành trình đóng/mở trung bình động (Moving Average Stroke Times) về 0 |
| **`MCmd_ClearTotTimes`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Xóa tổng thời gian tích lũy ở các trạng thái của van về 0 |
| **`MCmd_ClearMaxTimes`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Xóa thống kê thời gian đóng/mở/dừng cực đại của van về 0 |
| **`MCmd_ClearStrokeCounts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Xóa bộ đếm tổng số chu kỳ hành trình đóng/mở của van về 0 |
| **`MCmd_ClearSlowCounts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Xóa bộ đếm số lần van đóng/mở chậm vượt ngưỡng về 0 |
| **`MCmd_ClearMAvgs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Xóa thống kê thời gian hành trình đóng/mở trung bình động (Moving Average Stroke Times) về 0 |

## 3. Bảng chi tiết 34 Biến Đầu Ra (`Output Parameters`) của `P_ValveStats`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val_CurrClosedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian van đã duy trì ở trạng thái Đóng hoàn toàn trong lần đóng hiện tại (giây) |
| **`Val_LastClosedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian van đã lưu lại ở trạng thái Đóng trong lần đóng gần nhất trước đó (giây) |
| **`Val_TotClosedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng thời gian tích lũy van ở trạng thái Đóng hoàn toàn (giờ) |
| **`Val_MaxClosedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian lưu ở trạng thái Đóng lâu nhất từng ghi nhận được (giờ) |
| **`Val_CurrOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian van đang di chuyển Mở trong hành trình hiện tại (giây) |
| **`Val_LastOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian hành trình Mở thực tế của lần mở van gần nhất (giây) |
| **`Val_TotOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng thời gian tích lũy van đang trong hành trình di chuyển Mở (giờ) |
| **`Val_MaxOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian thực hiện hành trình Mở lâu nhất từng ghi nhận được (giây) |
| **`Val_MAvgOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian hành trình Mở trung bình động (Moving Average) của 10 lần mở van hoàn chỉnh gần nhất (giây) |
| **`Val_CurrOpenedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian van đã duy trì ở trạng thái Mở hoàn toàn trong lần mở hiện tại (giây) |
| **`Val_LastOpenedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian van đã lưu lại ở trạng thái Mở trong lần mở gần nhất trước đó (giây) |
| **`Val_TotOpenedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng thời gian tích lũy van ở trạng thái Mở hoàn toàn (giờ) |
| **`Val_MaxOpenedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian lưu ở trạng thái Mở lâu nhất từng ghi nhận được (giờ) |
| **`Val_CurrClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian van đang di chuyển Đóng trong hành trình hiện tại (giây) |
| **`Val_LastClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian hành trình Đóng thực tế của lần đóng van gần nhất (giây) |
| **`Val_TotClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng thời gian tích lũy van đang trong hành trình di chuyển Đóng (giờ) |
| **`Val_MaxClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian thực hiện hành trình Đóng lâu nhất từng ghi nhận được (giây) |
| **`Val_MAvgClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian hành trình Đóng trung bình động (Moving Average) của 10 lần đóng van hoàn chỉnh gần nhất (giây) |
| **`Val_CurrStopOtherT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian van đang ở trạng thái Dừng giữa chừng / trạng thái khác hiện tại (giây) |
| **`Val_LastStopOtherT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian van ở trạng thái Dừng giữa chừng trong lần gần nhất trước đó (giây) |
| **`Val_TotStopOtherT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng thời gian tích lũy van ở trạng thái Dừng giữa chừng / trạng thái khác (giờ) |
| **`Val_MaxStopOtherT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Thời gian lưu ở trạng thái Dừng giữa chừng lâu nhất từng ghi nhận được (giờ) |
| **`Val_CpltOpenC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lần van thực hiện hành trình Mở hoàn chỉnh (từ Đóng hoàn toàn sang Mở hoàn toàn) |
| **`Val_CpltCloseC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lần van thực hiện hành trình Đóng hoàn chỉnh (từ Mở hoàn toàn sang Đóng hoàn toàn) |
| **`Val_IncpltOpenC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lần van thực hiện hành trình Mở không hoàn chỉnh (đang Mở dở thì quay lại Đóng) |
| **`Val_IncpltCloseC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lần van thực hiện hành trình Đóng không hoàn chỉnh (đang Đóng dở thì quay lại Mở) |
| **`Val_StopOtherC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lần van chuyển sang trạng thái Dừng giữa hành trình hoặc trạng thái phụ khác |
| **`Val_SlowOpenC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lần van bị báo Mở chậm vượt quá ngưỡng thời gian cài đặt (Cfg_SlowOpenT) |
| **`Val_SlowCloseC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lần van bị báo Đóng chậm vượt quá ngưỡng thời gian cài đặt (Cfg_SlowCloseT) |
| **`Sts_SlowOpen`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hành trình Mở van hoàn chỉnh gần nhất có thời gian vượt quá ngưỡng cho phép (Cfg_SlowOpenT) |
| **`Sts_SlowClose`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hành trình Đóng van hoàn chỉnh gần nhất có thời gian vượt quá ngưỡng cho phép (Cfg_SlowCloseT) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`P_ValveStats`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_ValveStats) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 8 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ValveStats`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'HMI'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Valve Statistics (stroke times, counts)'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Valve Statistics'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_StopOtherDesc`** | `STRING_20` | `''` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI cho trạng thái Dừng giữa chừng hoặc trạng thái phụ của van |
| **`Cfg_Tag`** | `STRING_20` | `'P_ValveStats'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ValveStats'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
