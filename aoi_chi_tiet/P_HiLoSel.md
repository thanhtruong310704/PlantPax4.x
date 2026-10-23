# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_HiLoSel`
**Thuộc chương:** [Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)](../02_CHUONG_2_REGULATORY_PROCEDURAL_CONTROL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 89–91)  
**Tệp định nghĩa khối lệnh:** `P_HiLoSel_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_HiLoSel`:** **46** Biến đầu vào (`Input Parameters`) | **21** Biến đầu ra (`Output Parameters`) | **17** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_HiLoSel` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Chọn giá trị Cao nhất hoặc Thấp nhất (High/Low Selector)** từ tối đa **6 tín hiệu đầu vào CV** (`Inp_CV1..6`) để xuất ra 1 cơ cấu chấp hành chung (`Out_CV`), đồng thời xuất tín hiệu bám (`Out_CVxTrack`) trả ngược về các bộ PID không được chọn để chống bão hòa tích phân (Anti-Reset Windup).
- **Khi nào CẦN sử dụng:** **Bài toán điều khiển giành quyền bảo vệ (Override / Constraint Control):** Ví dụ 1 bơm biến tần bình thường chạy theo PID lưu lượng, nhưng có thêm PID bảo vệ áp suất đầu đẩy không quá cao và PID bảo vệ áp suất đầu hút không quá thấp. `P_HiLoSel` (chế độ Low Select) sẽ tự động chọn đầu ra nhỏ nhất để bảo vệ đường ống và bơm.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi muốn chọn Min/Max của các **cảm biến đầu vào PV** (trước khi vào PID) → Dùng [`P_AInDual.md`](./P_AInDual.md) hoặc [`P_AInMulti.md`](./P_AInMulti.md).

### Quy trình 4 bước đấu nối và cấu hình `P_HiLoSel`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_1`, `Inp_E1`, `Inp_Kp1`, `Inp_2`, `Inp_E2`, `Inp_Kp2`, `Inp_3`, `Inp_E3`, `Inp_Kp3`, `Inp_4`, `Inp_E4`, `Inp_Kp4`, `Inp_5`, `Inp_E5`, `Inp_Kp5`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HiLoSel`, `Cfg_HasInp1`, `Cfg_UseInp1`, `Cfg_Inp1Offset`, `Cfg_HasInp2`, `Cfg_UseInp2`, `Cfg_Inp2Offset`, `Cfg_HasInp3`, `Cfg_UseInp3`, `Cfg_Inp3Offset`, `Cfg_HasInp4`, `Cfg_UseInp4`, `Cfg_Inp4Offset`, `Cfg_HasInp5`, `Cfg_UseInp5`, `Cfg_Inp5Offset`, `Cfg_HasInp6`, `Cfg_UseInp6`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Inp1_Label`, `Cfg_Inp2_Label`, `Cfg_Inp3_Label`, `Cfg_Inp4_Label`, `Cfg_Inp5_Label`, `Cfg_Inp6_Label`, `Cfg_Label`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_HiLoSel` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_HiLoSel` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_HiLoSel` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Inp1Prev`, `Out_UseInp1Prev`, `Out_Inp2Prev`, `Out_UseInp2Prev`, `Out_Inp3Prev`, `Out_UseInp3Prev`, `Out_Inp4Prev`, `Out_UseInp4Prev`, `Out_Inp5Prev`, `Out_UseInp5Prev`, `Out_Inp6Prev`, `Out_UseInp6Prev`, `Val_Out`, `Val_Selected`, `Sts_MaintByp`, `Sts_Err`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 46 Biến Đầu Vào (`Input Parameters`) của `P_HiLoSel`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào số #1 đưa vào bộ chọn Cao/Thấp (P_HiLoSel) |
| **`Inp_E1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Sai lệch điều khiển (Loop Error) từ bộ PID sơ cấp số #1 (tùy chọn để tính độ lệch Offset chống vọt lố) |
| **`Inp_Kp1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Hệ số khuếch đại tỷ lệ (Proportional Gain Kp) từ bộ PID sơ cấp số #1 (tùy chọn để tính độ lệch Offset) |
| **`Inp_2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào số #2 đưa vào bộ chọn Cao/Thấp (P_HiLoSel) |
| **`Inp_E2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Sai lệch điều khiển (Loop Error) từ bộ PID sơ cấp số #2 (tùy chọn để tính độ lệch Offset chống vọt lố) |
| **`Inp_Kp2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Hệ số khuếch đại tỷ lệ (Proportional Gain Kp) từ bộ PID sơ cấp số #2 (tùy chọn để tính độ lệch Offset) |
| **`Inp_3`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào số #3 đưa vào bộ chọn Cao/Thấp (P_HiLoSel) |
| **`Inp_E3`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Sai lệch điều khiển (Loop Error) từ bộ PID sơ cấp số #3 (tùy chọn để tính độ lệch Offset chống vọt lố) |
| **`Inp_Kp3`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Hệ số khuếch đại tỷ lệ (Proportional Gain Kp) từ bộ PID sơ cấp số #3 (tùy chọn để tính độ lệch Offset) |
| **`Inp_4`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào số #4 đưa vào bộ chọn Cao/Thấp (P_HiLoSel) |
| **`Inp_E4`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Sai lệch điều khiển (Loop Error) từ bộ PID sơ cấp số #4 (tùy chọn để tính độ lệch Offset chống vọt lố) |
| **`Inp_Kp4`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Hệ số khuếch đại tỷ lệ (Proportional Gain Kp) từ bộ PID sơ cấp số #4 (tùy chọn để tính độ lệch Offset) |
| **`Inp_5`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào số #5 đưa vào bộ chọn Cao/Thấp (P_HiLoSel) |
| **`Inp_E5`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Sai lệch điều khiển (Loop Error) từ bộ PID sơ cấp số #5 (tùy chọn để tính độ lệch Offset chống vọt lố) |
| **`Inp_Kp5`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Hệ số khuếch đại tỷ lệ (Proportional Gain Kp) từ bộ PID sơ cấp số #5 (tùy chọn để tính độ lệch Offset) |
| **`Inp_6`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào số #6 đưa vào bộ chọn Cao/Thấp (P_HiLoSel) |
| **`Inp_E6`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Sai lệch điều khiển (Loop Error) từ bộ PID sơ cấp số #6 (tùy chọn để tính độ lệch Offset chống vọt lố) |
| **`Inp_Kp6`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Hệ số khuếch đại tỷ lệ (Proportional Gain Kp) từ bộ PID sơ cấp số #6 (tùy chọn để tính độ lệch Offset) |
| **`Inp_InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị khởi tạo gửi ngược từ khối phía sau (Downstream Initialization Value) để đồng bộ không giật |
| **`Inp_InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Yêu cầu khởi tạo bám giá trị từ khối phía sau gửi ngược về (1 = Khởi tạo ngõ ra bằng giá trị Inp_InitVal) |
| **`Cfg_HiLoSel`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Chế độ chọn tín hiệu của khối P_HiLoSel: 1 = Chọn giá trị Lớn nhất (High-Select); 0 = Chọn giá trị Nhỏ nhất (Low-Select) |
| **`Cfg_HasInp1`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kênh đầu vào số #1 (Inp_1) có được kết nối sử dụng |
| **`Cfg_UseInp1`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh đầu vào số #1 (Inp_1) vào bộ chọn Min/Max; 0 = Tạm loại kênh #1 khỏi bộ chọn (Maintenance) |
| **`Cfg_Inp1Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cộng thêm độ lệch bù Kp1 × E1 vào giá trị khởi tạo trả về bộ PID sơ cấp #1 để chống vọt lố khi chuyển mạch; 0 = Không cộng Offset |
| **`Cfg_HasInp2`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kênh đầu vào số #2 (Inp_2) có được kết nối sử dụng |
| **`Cfg_UseInp2`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh đầu vào số #2 (Inp_2) vào bộ chọn Min/Max; 0 = Tạm loại kênh #2 khỏi bộ chọn (Maintenance) |
| **`Cfg_Inp2Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cộng thêm độ lệch bù Kp2 × E2 vào giá trị khởi tạo trả về bộ PID sơ cấp #2 để chống vọt lố khi chuyển mạch; 0 = Không cộng Offset |
| **`Cfg_HasInp3`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kênh đầu vào số #3 (Inp_3) có được kết nối sử dụng |
| **`Cfg_UseInp3`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh đầu vào số #3 (Inp_3) vào bộ chọn Min/Max; 0 = Tạm loại kênh #3 khỏi bộ chọn (Maintenance) |
| **`Cfg_Inp3Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cộng thêm độ lệch bù Kp3 × E3 vào giá trị khởi tạo trả về bộ PID sơ cấp #3 để chống vọt lố khi chuyển mạch; 0 = Không cộng Offset |
| **`Cfg_HasInp4`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kênh đầu vào số #4 (Inp_4) có được kết nối sử dụng |
| **`Cfg_UseInp4`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh đầu vào số #4 (Inp_4) vào bộ chọn Min/Max; 0 = Tạm loại kênh #4 khỏi bộ chọn (Maintenance) |
| **`Cfg_Inp4Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cộng thêm độ lệch bù Kp4 × E4 vào giá trị khởi tạo trả về bộ PID sơ cấp #4 để chống vọt lố khi chuyển mạch; 0 = Không cộng Offset |
| **`Cfg_HasInp5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kênh đầu vào số #5 (Inp_5) có được kết nối sử dụng |
| **`Cfg_UseInp5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh đầu vào số #5 (Inp_5) vào bộ chọn Min/Max; 0 = Tạm loại kênh #5 khỏi bộ chọn (Maintenance) |
| **`Cfg_Inp5Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cộng thêm độ lệch bù Kp5 × E5 vào giá trị khởi tạo trả về bộ PID sơ cấp #5 để chống vọt lố khi chuyển mạch; 0 = Không cộng Offset |
| **`Cfg_HasInp6`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kênh đầu vào số #6 (Inp_6) có được kết nối sử dụng |
| **`Cfg_UseInp6`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh đầu vào số #6 (Inp_6) vào bộ chọn Min/Max; 0 = Tạm loại kênh #6 khỏi bộ chọn (Maintenance) |
| **`Cfg_Inp6Offset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cộng thêm độ lệch bù Kp6 × E6 vào giá trị khởi tạo trả về bộ PID sơ cấp #6 để chống vọt lố khi chuyển mạch; 0 = Không cộng Offset |
| **`Cfg_DecPlcs`** | `SINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho tốc độ/giá trị trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasOutNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép hiển thị nút điều hướng nhảy tới Faceplate của khối thiết bị ngõ ra trên HMI |
| **`Cfg_HasNav`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit (16-bit) bật nút điều hướng nhảy tới Faceplate nguyên nhân cho từng điều kiện trong 16 kênh (Bit = 1 là bật) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_OutMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho ngõ ra của bộ chọn P_HiLoSel |
| **`Cfg_OutMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho ngõ ra của bộ chọn P_HiLoSel |

## 3. Bảng chi tiết 21 Biến Đầu Ra (`Output Parameters`) của `P_HiLoSel`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Out_Inp1Prev`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị phản hồi (Tieback/Feedback) trả ngược về bộ điều khiển sơ cấp số #1 để chống bão hòa tích phân |
| **`Out_UseInp1Prev`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ yêu cầu bộ điều khiển sơ cấp số #1 bám theo giá trị phản hồi Out_Inp1Prev (khi kênh #1 không được chọn) |
| **`Out_Inp2Prev`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị phản hồi (Tieback/Feedback) trả ngược về bộ điều khiển sơ cấp số #2 để chống bão hòa tích phân |
| **`Out_UseInp2Prev`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ yêu cầu bộ điều khiển sơ cấp số #2 bám theo giá trị phản hồi Out_Inp2Prev (khi kênh #2 không được chọn) |
| **`Out_Inp3Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị phản hồi (Tieback/Feedback) trả ngược về bộ điều khiển sơ cấp số #3 để chống bão hòa tích phân |
| **`Out_UseInp3Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ yêu cầu bộ điều khiển sơ cấp số #3 bám theo giá trị phản hồi Out_Inp3Prev (khi kênh #3 không được chọn) |
| **`Out_Inp4Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị phản hồi (Tieback/Feedback) trả ngược về bộ điều khiển sơ cấp số #4 để chống bão hòa tích phân |
| **`Out_UseInp4Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ yêu cầu bộ điều khiển sơ cấp số #4 bám theo giá trị phản hồi Out_Inp4Prev (khi kênh #4 không được chọn) |
| **`Out_Inp5Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị phản hồi (Tieback/Feedback) trả ngược về bộ điều khiển sơ cấp số #5 để chống bão hòa tích phân |
| **`Out_UseInp5Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ yêu cầu bộ điều khiển sơ cấp số #5 bám theo giá trị phản hồi Out_Inp5Prev (khi kênh #5 không được chọn) |
| **`Out_Inp6Prev`** | `REAL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị phản hồi (Tieback/Feedback) trả ngược về bộ điều khiển sơ cấp số #6 để chống bão hòa tích phân |
| **`Out_UseInp6Prev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ yêu cầu bộ điều khiển sơ cấp số #6 bám theo giá trị phản hồi Out_Inp6Prev (khi kênh #6 không được chọn) |
| **`Val_Out`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu ra đã được chọn (Min hoặc Max của các đầu vào) để hiển thị trên HMI |
| **`Val_Selected`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã số kênh đầu vào đang được bộ P_HiLoSel chọn xuất ra: 0 = Ngưỡng kẹp dưới (Min), 1..6 = Kênh Inp_1..Inp_6, 7 = Ngưỡng kẹp trên (Max), 8 = Giá trị khởi tạo Inp_InitVal |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Has`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Bắt buộc phải bật ít nhất một kênh cảm biến đầu vào (Cfg_HasPVx = 1) |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Ngưỡng kẹp giới hạn trên của PV nhỏ hơn ngưỡng kẹp giới hạn dưới (Cfg_PVHiLim < Cfg_PVLoLim) |
| **`P_HiLoSel`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_HiLoSel) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 17 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_HiLoSel`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'High or Low Selector'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật (Engineering Units, ví dụ: "°C", "bar", "m3/h", "%") hiển thị trên màn hình HMI |
| **`Cfg_Inp1_Label`** | `STRING_20` | `'Input 1'` | [Biến cấu hình thông số] Nhãn mô tả cho kênh đầu vào số #1 hiển thị trên HMI |
| **`Cfg_Inp2_Label`** | `STRING_20` | `'Input 2'` | [Biến cấu hình thông số] Nhãn mô tả cho kênh đầu vào số #2 hiển thị trên HMI |
| **`Cfg_Inp3_Label`** | `STRING_20` | `'Input 3'` | [Biến cấu hình thông số] Nhãn mô tả cho kênh đầu vào số #3 hiển thị trên HMI |
| **`Cfg_Inp4_Label`** | `STRING_20` | `'Input 4'` | [Biến cấu hình thông số] Nhãn mô tả cho kênh đầu vào số #4 hiển thị trên HMI |
| **`Cfg_Inp5_Label`** | `STRING_20` | `'Input 5'` | [Biến cấu hình thông số] Nhãn mô tả cho kênh đầu vào số #5 hiển thị trên HMI |
| **`Cfg_Inp6_Label`** | `STRING_20` | `'Input 6'` | [Biến cấu hình thông số] Nhãn mô tả cho kênh đầu vào số #6 hiển thị trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'High/Low Selector'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_NavTag`** | `STRING_NavTag[6]` | `{...}` | [Biến cấu hình thông số] Mảng 16 tên thẻ (Tagname) đích tương ứng với 16 nút điều hướng điều kiện liên động/cho phép trên HMI |
| **`Cfg_OutNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tên thẻ (Tagname) của khối thiết bị đầu ra đích khi nhấn nút điều hướng trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_HiLoSel'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_HasMore`** | `BOOL` | `0` | Cờ nội bộ báo cho HMI hiển thị nút mở rộng thông tin |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_HiLoSel'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
