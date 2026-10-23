# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Fanout`
**Thuộc chương:** [Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)](../02_CHUONG_2_REGULATORY_PROCEDURAL_CONTROL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 84–88)  
**Tệp định nghĩa khối lệnh:** `P_Fanout_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Fanout`:** **63** Biến đầu vào (`Input Parameters`) | **61** Biến đầu ra (`Output Parameters`) | **26** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Fanout` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Phân nhánh tín hiệu tương tự (Analog Fanout / Split-Range)** từ 1 đầu vào sơ cấp (`Inp_CV`) ra tối đa **8 đầu ra thứ cấp** (`Out_CV1..8`). Mỗi nhánh có hệ số tỷ lệ (Gain/Slope $m_i$), độ dịch (Offset $b_i$) và kẹp giới hạn riêng, đồng thời cho phép tách lẻ từng nhánh sang điều khiển bằng tay trên HMI.
- **Khi nào CẦN sử dụng:** 1. **Điều khiển chia dải (Split-range):** 1 bộ PID nhiệt độ điều khiển cả van cấp nhiệt (mở khi CV từ `50..100%`) và van nước làm mát (mở khi CV từ `50..0%`). 2. **Điều khiển đồng tốc:** 1 bộ PID áp suất điều khiển đồng thời 4 biến tần quạt gió chạy song song.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi chỉ có 1 bộ PID điều khiển 1 cơ cấu chấp hành duy nhất.

### Quy trình 4 bước đấu nối và cấu hình `P_Fanout`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_CV`, `Inp_CV1InitVal`, `Inp_CV1InitReq`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasCV2`, `Cfg_HasCV3`, `Cfg_HasCV4`, `Cfg_HasCV5`, `Cfg_HasCV6`, `Cfg_HasCV7`, `Cfg_HasCV8`, `Cfg_HasMoreObj`, `Cfg_FixedInitVal`, `Cfg_UseFixedInit`, `Cfg_ShedHold`, `Cfg_HasCVNav`, `Cfg_HasNav`, `Cfg_CVEUMin`, `Cfg_CVEUMax`, `Cfg_CVMin`, `Cfg_CVMax`, `Cfg_CVRoCLim`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_CV_EU`, `Cfg_CV1_EU`, `Cfg_CV1_Label`, `Cfg_CV2_EU`, `Cfg_CV2_Label`, `Cfg_CV3_EU`, `Cfg_CV3_Label`, `Cfg_CV4_EU`, `Cfg_CV4_Label`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Fanout` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Fanout` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_Fanout` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_CV1`, `Out_CV2`, `Out_CV3`, `Out_CV4`, `Out_CV5`, `Out_CV6`, `Out_CV7`, `Out_CV8`, `Out_CVInitVal`, `Out_CVInitReq`, `Val_CVEUMin`, `Val_CVEUMax`, `Val_InpCV`, `Val_CV`, `Val_MinCVIn1`, `Val_MinCVIn2`, `Val_MinCVIn3`, `Val_MinCVIn4`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 63 Biến Đầu Vào (`Input Parameters`) của `P_Fanout`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_CV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều khiển đầu vào (Input CV, đơn vị kỹ thuật EU) nhận từ ngõ ra của khối điều khiển phía trước (Upstream block) |
| **`Inp_CV1InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị khởi tạo gửi ngược từ khối chấp hành thứ cấp số #1 (đơn vị kỹ thuật của nhánh CV1) |
| **`Inp_CV1InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Yêu cầu khởi tạo bám giá trị từ khối chấp hành thứ cấp số #1 (1 = Đang yêu cầu khởi tạo nhánh CV1) |
| **`Cfg_HasCV2`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV2 có được kết nối và sử dụng |
| **`Cfg_HasCV3`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV3 có được kết nối và sử dụng |
| **`Cfg_HasCV4`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV4 có được kết nối và sử dụng |
| **`Cfg_HasCV5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV5 có được kết nối và sử dụng |
| **`Cfg_HasCV6`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV6 có được kết nối và sử dụng |
| **`Cfg_HasCV7`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV7 có được kết nối và sử dụng |
| **`Cfg_HasCV8`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhánh ngõ ra thứ cấp CV8 có được kết nối và sử dụng |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_FixedInitVal`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị khởi tạo cố định (đơn vị Inp_CV EU) gửi về khối sơ cấp khi Cfg_UseFixedInit = 1 |
| **`Cfg_UseFixedInit`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng hằng số Cfg_FixedInitVal để khởi tạo khối sơ cấp; 0 = Sử dụng giá trị phản hồi từ nhánh CV1 (Inp_CV1InitVal) |
| **`Cfg_ShedHold`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra CV khi vi phạm Khóa liên động (Interlock Trip): 1 = Giữ nguyên giá trị CV hiện tại (Hold Last State); 0 = Đưa ngõ ra CV về giá trị an toàn định trước (Cfg_IntlkCV) |
| **`Cfg_HasCVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép hiển thị nút điều hướng nhảy tới Faceplate của khối cơ cấu chấp hành CV trên HMI |
| **`Cfg_HasNav`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit (16-bit) bật nút điều hướng nhảy tới Faceplate nguyên nhân cho từng điều kiện trong 16 kênh (Bit = 1 là bật) |
| **`Cfg_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật đầu ra CV tương ứng với Cfg_CVRawMin (thường là 0.0%) |
| **`Cfg_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật đầu ra CV tương ứng với Cfg_CVRawMax (thường là 100.0%) |
| **`Cfg_CVMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu của tín hiệu CV (đơn vị kỹ thuật EU) |
| **`Cfg_CVMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa của tín hiệu CV (đơn vị kỹ thuật EU) |
| **`Cfg_CVRoCLim`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn tốc độ biến thiên tối đa của ngõ ra điều khiển CV (CV Rate-of-Change Limit, đơn vị CVEU/giây) |
| **`Cfg_CV1Ratio`** | `REAL` | `-2` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV1 (Out_CV1 = Inp_CV × Ratio1 + Offset1) |
| **`Cfg_CV2Ratio`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV2 (Out_CV2 = Inp_CV × Ratio2 + Offset2) |
| **`Cfg_CV3Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV3 (Out_CV3 = Inp_CV × Ratio3 + Offset3) |
| **`Cfg_CV4Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV4 (Out_CV4 = Inp_CV × Ratio4 + Offset4) |
| **`Cfg_CV5Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV5 (Out_CV5 = Inp_CV × Ratio5 + Offset5) |
| **`Cfg_CV6Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV6 (Out_CV6 = Inp_CV × Ratio6 + Offset6) |
| **`Cfg_CV7Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV7 (Out_CV7 = Inp_CV × Ratio7 + Offset7) |
| **`Cfg_CV8Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số tỷ lệ (Gain/Ratio) nhân với Inp_CV để tính nhánh ngõ ra CV8 (Out_CV8 = Inp_CV × Ratio8 + Offset8) |
| **`Cfg_CV1Offset`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV1 (đơn vị kỹ thuật EU) |
| **`Cfg_CV2Offset`** | `REAL` | `-100` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV2 (đơn vị kỹ thuật EU) |
| **`Cfg_CV3Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV3 (đơn vị kỹ thuật EU) |
| **`Cfg_CV4Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV4 (đơn vị kỹ thuật EU) |
| **`Cfg_CV5Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV5 (đơn vị kỹ thuật EU) |
| **`Cfg_CV6Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV6 (đơn vị kỹ thuật EU) |
| **`Cfg_CV7Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV7 (đơn vị kỹ thuật EU) |
| **`Cfg_CV8Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm (Offset/Bias) khi tính nhánh ngõ ra CV8 (đơn vị kỹ thuật EU) |
| **`Cfg_CV1Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV1 (đơn vị kỹ thuật EU) |
| **`Cfg_CV2Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV2 (đơn vị kỹ thuật EU) |
| **`Cfg_CV3Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV3 (đơn vị kỹ thuật EU) |
| **`Cfg_CV4Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV4 (đơn vị kỹ thuật EU) |
| **`Cfg_CV5Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV5 (đơn vị kỹ thuật EU) |
| **`Cfg_CV6Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV6 (đơn vị kỹ thuật EU) |
| **`Cfg_CV7Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV7 (đơn vị kỹ thuật EU) |
| **`Cfg_CV8Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho nhánh ngõ ra CV8 (đơn vị kỹ thuật EU) |
| **`Cfg_CV1Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV1 (đơn vị kỹ thuật EU) |
| **`Cfg_CV2Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV2 (đơn vị kỹ thuật EU) |
| **`Cfg_CV3Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV3 (đơn vị kỹ thuật EU) |
| **`Cfg_CV4Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV4 (đơn vị kỹ thuật EU) |
| **`Cfg_CV5Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV5 (đơn vị kỹ thuật EU) |
| **`Cfg_CV6Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV6 (đơn vị kỹ thuật EU) |
| **`Cfg_CV7Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV7 (đơn vị kỹ thuật EU) |
| **`Cfg_CV8Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho nhánh ngõ ra CV8 (đơn vị kỹ thuật EU) |
| **`Cfg_CV1TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV1 sau khi kết thúc khởi tạo |
| **`Cfg_CV2TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV2 sau khi kết thúc khởi tạo |
| **`Cfg_CV3TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV3 sau khi kết thúc khởi tạo |
| **`Cfg_CV4TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV4 sau khi kết thúc khởi tạo |
| **`Cfg_CV5TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV5 sau khi kết thúc khởi tạo |
| **`Cfg_CV6TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV6 sau khi kết thúc khởi tạo |
| **`Cfg_CV7TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV7 sau khi kết thúc khởi tạo |
| **`Cfg_CV8TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Tốc độ thu hồi độ lệch (Takeup Rate, đơn vị EU/giây) để đồng bộ êm nhánh CV8 sau khi kết thúc khởi tạo |
| **`Cfg_CVDecPlcs`** | `SINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị ngõ ra CV trên màn hình HMI (từ 0 đến 6 chữ số) |

## 3. Bảng chi tiết 61 Biến Đầu Ra (`Output Parameters`) của `P_Fanout`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_CV1`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV1 (đơn vị kỹ thuật EU, tính theo công thức Out_CV1 = Inp_CV × Ratio1 + Offset1) |
| **`Out_CV2`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV2 (đơn vị kỹ thuật EU, tính theo công thức Out_CV2 = Inp_CV × Ratio2 + Offset2) |
| **`Out_CV3`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV3 (đơn vị kỹ thuật EU, tính theo công thức Out_CV3 = Inp_CV × Ratio3 + Offset3) |
| **`Out_CV4`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV4 (đơn vị kỹ thuật EU, tính theo công thức Out_CV4 = Inp_CV × Ratio4 + Offset4) |
| **`Out_CV5`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV5 (đơn vị kỹ thuật EU, tính theo công thức Out_CV5 = Inp_CV × Ratio5 + Offset5) |
| **`Out_CV6`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV6 (đơn vị kỹ thuật EU, tính theo công thức Out_CV6 = Inp_CV × Ratio6 + Offset6) |
| **`Out_CV7`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV7 (đơn vị kỹ thuật EU, tính theo công thức Out_CV7 = Inp_CV × Ratio7 + Offset7) |
| **`Out_CV8`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển nhánh thứ cấp CV8 (đơn vị kỹ thuật EU, tính theo công thức Out_CV8 = Inp_CV × Ratio8 + Offset8) |
| **`Out_CVInitVal`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị khởi tạo (Initialization Value, đơn vị EU) gửi ngược lên khối điều khiển phía trước để chuyển mạch không giật |
| **`Out_CVInitReq`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Yêu cầu khởi tạo bám giá trị (Initialization Request: 1 = Yêu cầu khởi tạo) gửi ngược lên khối điều khiển phía trước (Upstream block) |
| **`Val_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đơn vị kỹ thuật ngõ ra CV = MIN(Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đơn vị kỹ thuật ngõ ra CV = MAX(Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_InpCV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tín hiệu đầu vào Inp_CV trước khi kẹp giới hạn hoặc giới hạn tốc độ (đơn vị EU) |
| **`Val_CV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến điều khiển CV hiện hành (sau khi đã kẹp giới hạn và giới hạn tốc độ biến thiên, đơn vị EU) |
| **`Val_MinCVIn1`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV1 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn2`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV2 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn3`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV3 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn4`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV4 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn5`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV5 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn6`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV6 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn7`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV7 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MinCVIn8`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV8 chạm ngưỡng kẹp tối thiểu (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn1`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV1 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn2`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV2 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn3`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV3 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn4`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV4 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn5`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV5 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn6`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV6 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn7`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV7 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Val_MaxCVIn8`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào Inp_CV tương ứng với lúc nhánh ngõ ra CV8 chạm ngưỡng kẹp tối đa (dùng để vẽ đồ thị đặc tuyến trên HMI) |
| **`Sts_CVInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào Inp_CV là số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CVLimited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Val_CV đã chạm và bị giới hạn tại ngưỡng Cfg_MinCV hoặc Cfg_MaxCV |
| **`Sts_CV1InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV1InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV1Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV1 đang bị kẹp giới hạn tại ngưỡng Cfg_CV1Min hoặc Cfg_CV1Max |
| **`Sts_CV2InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV2InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV2Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV2 đang bị kẹp giới hạn tại ngưỡng Cfg_CV2Min hoặc Cfg_CV2Max |
| **`Sts_CV3InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV3InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV3Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV3 đang bị kẹp giới hạn tại ngưỡng Cfg_CV3Min hoặc Cfg_CV3Max |
| **`Sts_CV4InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV4InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV4Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV4 đang bị kẹp giới hạn tại ngưỡng Cfg_CV4Min hoặc Cfg_CV4Max |
| **`Sts_CV5InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV5InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV5Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV5 đang bị kẹp giới hạn tại ngưỡng Cfg_CV5Min hoặc Cfg_CV5Max |
| **`Sts_CV6InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV6InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV6Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV6 đang bị kẹp giới hạn tại ngưỡng Cfg_CV6Min hoặc Cfg_CV6Max |
| **`Sts_CV7InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV7InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV7Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV7 đang bị kẹp giới hạn tại ngưỡng Cfg_CV7Min hoặc Cfg_CV7Max |
| **`Sts_CV8InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị khởi tạo Inp_CV8InitVal bị lỗi số vô cùng (Infinite) hoặc không phải là số (NaN) |
| **`Sts_CV8Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra nhánh CV8 đang bị kẹp giới hạn tại ngưỡng Cfg_CV8Min hoặc Cfg_CV8Max |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Ngưỡng kẹp giới hạn trên của PV nhỏ hơn ngưỡng kẹp giới hạn dưới (Cfg_PVHiLim < Cfg_PVLoLim) |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_CV1Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV1 bị ngược (Cfg_CV1Max < Cfg_CV1Min) |
| **`Err_CV2Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV2 bị ngược (Cfg_CV2Max < Cfg_CV2Min) |
| **`Err_CV3Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV3 bị ngược (Cfg_CV3Max < Cfg_CV3Min) |
| **`Err_CV4Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV4 bị ngược (Cfg_CV4Max < Cfg_CV4Min) |
| **`Err_CV5Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV5 bị ngược (Cfg_CV5Max < Cfg_CV5Min) |
| **`Err_CV6Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV6 bị ngược (Cfg_CV6Max < Cfg_CV6Min) |
| **`Err_CV7Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV7 bị ngược (Cfg_CV7Max < Cfg_CV7Min) |
| **`Err_CV8Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp biên độ của ngõ ra nhánh CV8 bị ngược (Cfg_CV8Max < Cfg_CV8Min) |
| **`P_Fanout`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Fanout) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 26 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Fanout`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_CV_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của ngõ ra điều khiển CV hiển thị trên màn hình HMI |
| **`Cfg_CV1_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #1 (CV1) hiển thị trên HMI |
| **`Cfg_CV1_Label`** | `STRING_20` | `'Output 1'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #1 (CV1) hiển thị trên HMI |
| **`Cfg_CV2_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #2 (CV2) hiển thị trên HMI |
| **`Cfg_CV2_Label`** | `STRING_20` | `'Output 2'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #2 (CV2) hiển thị trên HMI |
| **`Cfg_CV3_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #3 (CV3) hiển thị trên HMI |
| **`Cfg_CV3_Label`** | `STRING_20` | `'Output 3'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #3 (CV3) hiển thị trên HMI |
| **`Cfg_CV4_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #4 (CV4) hiển thị trên HMI |
| **`Cfg_CV4_Label`** | `STRING_20` | `'Output 4'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #4 (CV4) hiển thị trên HMI |
| **`Cfg_CV5_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #5 (CV5) hiển thị trên HMI |
| **`Cfg_CV5_Label`** | `STRING_20` | `'Output 5'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #5 (CV5) hiển thị trên HMI |
| **`Cfg_CV6_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #6 (CV6) hiển thị trên HMI |
| **`Cfg_CV6_Label`** | `STRING_20` | `'Output 6'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #6 (CV6) hiển thị trên HMI |
| **`Cfg_CV7_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #7 (CV7) hiển thị trên HMI |
| **`Cfg_CV7_Label`** | `STRING_20` | `'Output 7'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #7 (CV7) hiển thị trên HMI |
| **`Cfg_CV8_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhánh ngõ ra số #8 (CV8) hiển thị trên HMI |
| **`Cfg_CV8_Label`** | `STRING_20` | `'Output 8'` | [Biến cấu hình thông số] Nhãn tên của nhánh ngõ ra số #8 (CV8) hiển thị trên HMI |
| **`Cfg_CVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tên thẻ (Tagname) của khối cơ cấu chấp hành CV đích khi nhấn nút điều hướng CV trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Fanout'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Analog Fanout'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_NavTag`** | `STRING_NavTag[8]` | `{...}` | [Biến cấu hình thông số] Mảng 16 tên thẻ (Tagname) đích tương ứng với 16 nút điều hướng điều kiện liên động/cho phép trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_Fanout'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Fanout'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
