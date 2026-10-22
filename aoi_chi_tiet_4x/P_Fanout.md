# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Fanout`
**Thuộc chương:** Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Fanout`:** **77** Biến đầu vào (`Input Parameters`) | **61** Biến đầu ra (`Output Parameters`) | **26** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_Fanout`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_Fanout_4.10.00_AOI.L5X` *(Trang 84–88 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Phân nhánh tín hiệu tương tự (Analog Fanout / Split-Range)** từ 1 đầu vào sơ cấp (`Inp_CV`) ra tối đa **8 đầu ra thứ cấp** (`Out_CV1..8`). Mỗi nhánh có hệ số tỷ lệ (Gain/Slope $m_i$), độ dịch (Offset $b_i$) và kẹp giới hạn riêng, đồng thời cho phép tách lẻ từng nhánh sang điều khiển bằng tay trên HMI.
- **Các nhu cầu thực tế CẦN dùng khối `P_Fanout`:** 1. **Điều khiển chia dải (Split-range):** 1 bộ PID nhiệt độ điều khiển cả van cấp nhiệt (mở khi CV từ `50..100%`) và van nước làm mát (mở khi CV từ `50..0%`). 2. **Điều khiển đồng tốc:** 1 bộ PID áp suất điều khiển đồng thời 4 biến tần quạt gió chạy song song.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi chỉ có 1 bộ PID điều khiển 1 cơ cấu chấp hành duy nhất.

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Fanout`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_CV`, `Inp_CV1InitVal`, `Inp_CV2InitVal`, `Inp_CV3InitVal`, `Inp_CV4InitVal`, `Inp_CV5InitVal`, `Inp_CV6InitVal`, `Inp_CV7InitVal`, `Inp_CV8InitVal`, `Inp_CV1InitReq`, `Inp_CV2InitReq`, `Inp_CV3InitReq`, `Inp_CV4InitReq`, `Inp_CV5InitReq`, `Inp_CV6InitReq`, `Inp_CV7InitReq`, `Inp_CV8InitReq`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasCV2`, `Cfg_HasCV3`, `Cfg_HasCV4`, `Cfg_HasCV5`, `Cfg_HasCV6`, `Cfg_HasCV7`, `Cfg_HasCV8`, `Cfg_HasMoreObj`, `Cfg_FixedInitVal`, `Cfg_UseFixedInit`, `Cfg_ShedHold`, `Cfg_HasCVNav`, `Cfg_HasNav`, `Cfg_CVEUMin`, `Cfg_CVEUMax`, `Cfg_CVMin`, `Cfg_CVMax`, `Cfg_CVRoCLim`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_CV_EU`, `Cfg_CV1_EU`, `Cfg_CV1_Label`, `Cfg_CV2_EU`, `Cfg_CV2_Label`, `Cfg_CV3_EU`, `Cfg_CV3_Label`, `Cfg_CV4_EU`, `Cfg_CV4_Label`, `Cfg_CV5_EU`, `Cfg_CV5_Label`, `Cfg_CV6_EU`, `Cfg_CV6_Label`, `Cfg_CV7_EU`, `Cfg_CV7_Label`, `Cfg_CV8_EU`, `Cfg_CV8_Label`, `Cfg_CVNavTag`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Fanout` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_CV1`, `Out_CV2`, `Out_CV3`, `Out_CV4`, `Out_CV5`, `Out_CV6`, `Out_CV7`, `Out_CV8`, `Out_CVInitVal`, `Out_CVInitReq`, `Val_CVEUMin`, `Val_CVEUMax`, `Val_InpCV`, `Val_CV`, `Val_MinCVIn1`, `Val_MinCVIn2`, `Val_MinCVIn3`, `Val_MinCVIn4`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 77 Biến Đầu Vào (`Input Parameters`) của `P_Fanout`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_CV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input CV from Upstream block's output (EU) |
| **`Inp_CV1InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV2InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV3InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV4InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV5InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV6InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV7InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV8InitVal`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization value from downstream block 1…8 (in the associated CV engineering units). |
| **`Inp_CV1InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV2InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV3InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV4InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV5InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV6InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV7InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Inp_CV8InitReq`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Initialization request from downstream block 1…8. When these parameters are set to 1, the corresponding CV output is set to the value of the corresponding initialization input. For example, if Inp_CV1InitReq is 1, Out_CV1 is set to Inp_CV1InitiVal. When all configured CVs have their initialization request set, Out_CVInitReq is set to 1 and Out_CVInitVal is set to a value based on Inp_CV1InitVal and the ratio and offset for CV1. |
| **`Cfg_HasCV2`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasCV3`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasCV4`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasCV5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasCV6`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasCV7`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasCV8`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Output CV2 …CV8 are connected and are to be used. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_FixedInitVal`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Fixed Initialization value (in Inp_CV eng. units), used if Cfg_UseFixedInit=1 |
| **`Cfg_UseFixedInit`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Use Cfg_FixedInitVal to initialize primary, 0=Use Inp_CV1InitVal |
| **`Cfg_ShedHold`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Hold Output on Inf / NaN Input, 0=Copy Inf / NaN through |
| **`Cfg_HasCVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI to enable navigation to a connected input CV object |
| **`Cfg_HasNav`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set bits indicate which Navigation buttons are enabled .0=CV1, .1=CV2, ... .7=CV8 |
| **`Cfg_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to the range of the signal connected to Inp_CV. |
| **`Cfg_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to the range of the signal connected to Inp_CV. |
| **`Cfg_CVMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Input CV minimum (Lo Clamp) or CV maximum (Hi Clamp) (in engineering units). |
| **`Cfg_CVMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Input CV minimum (Lo Clamp) or CV maximum (Hi Clamp) (in engineering units). |
| **`Cfg_CVRoCLim`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Program Setting for Input CV Rate of Change Limit (inc or dec) (Inp EU/sec) |
| **`Cfg_CV1Ratio`** | `REAL` | `-2` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV2Ratio`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV3Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV4Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV5Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV6Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV7Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV8Ratio`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 ratios. These parameters are used to calculate the corresponding output when the ratio source for the output is configuration. For example, Cfg_CV1Ratio is used to calculate Out_CV1 when Cfg_CV1RatioSrc = 0. The selected ratio is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV1Offset`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV2Offset`** | `REAL` | `-100` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV3Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV4Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV5Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV6Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV7Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV8Offset`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Configuration for CV1…CV8 offsets. These parameters are used to calculate the corresponding output when the offset source for the output is configuration. For example, Cfg_CV1Offset is used to calculate Out_CV1 when Cfg_CV1OffsetSrc = 0. The selected offset is stored in output parameters and the instruction uses the output parameters for the calculations (for example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset). |
| **`Cfg_CV1Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV2Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV3Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV4Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV5Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV6Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV7Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV8Min`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 minimum in engineering units (for clamping). |
| **`Cfg_CV1Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV2Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV3Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV4Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV5Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV6Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV7Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV8Max`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Output CV1…CV8 maximum in engineering units (for clamping). |
| **`Cfg_CV1TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV2TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV3TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV4TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV5TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV6TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV7TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CV8TakeupRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate (in engineering units/second) which CV1…CV8 bias is taken up after the corresponding Inp_CVnInitReq clears to 0. |
| **`Cfg_CVDecPlcs`** | `SINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for CV display (0..6) |

## 3. Bảng chi tiết 61 Biến Đầu Ra (`Output Parameters`) của `P_Fanout`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Output - System Defined Parameter |
| **`Out_CV1`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV2`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV3`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV4`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV5`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV6`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV7`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CV8`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Calculated outputs in engineering units. For example, Out_CV1 = Inp_CV * Val_CV1Ratio + Val_CV1Offset. |
| **`Out_CVInitVal`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Initialization Value to Upstream block (Inp_CV EU) |
| **`Out_CVInitReq`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Iniitialization Request to Upstream block (1=Initialize) |
| **`Val_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of scaled range = MIN (Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of scaled range = MAX (Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_InpCV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of Inp_CV (not clamped or ramped) (EU) |
| **`Val_CV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of CV after clamping and ramping (EU) |
| **`Val_MinCVIn1`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn2`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn3`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn4`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn5`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn6`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn7`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MinCVIn8`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at minimum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn1`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn2`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn3`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn4`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn5`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn6`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn7`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Val_MaxCVIn8`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Input CV at maximum of CV1…CV8 outputs. These are used to display the configuration in graph form on the faceplate. |
| **`Sts_CVInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV is Infinite or Not A Number (1.$, 1.#NaN) |
| **`Sts_CVLimited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Val_CV (selected CV) clamped at configured Max/Min |
| **`Sts_CV1InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV1InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV1Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV1 clamped at configured Max/Min |
| **`Sts_CV2InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV2InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV2Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV2 clamped at configured Max/Min |
| **`Sts_CV3InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV3InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV3Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV3 clamped at configured Max/Min |
| **`Sts_CV4InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV4InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV4Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV4 clamped at configured Max/Min |
| **`Sts_CV5InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV5InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV5Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV5 clamped at configured Max/Min |
| **`Sts_CV6InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV6InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV6Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV6 clamped at configured Max/Min |
| **`Sts_CV7InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV7InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV7Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV7 clamped at configured Max/Min |
| **`Sts_CV8InitInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_CV8InitVal is Infinite or Not a Number (1.$, 1.#NaN) |
| **`Sts_CV8Limited`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Output CV8 clamped at configured Max/Min |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV Clamp Limits crossed (Max<Min) |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV Scale EU Min = Max |
| **`Err_CV1Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV1 Clamp Limits crossed (Max<Min) |
| **`Err_CV2Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV2 Clamp Limits crossed (Max<Min) |
| **`Err_CV3Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV3 Clamp Limits crossed (Max<Min) |
| **`Err_CV4Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV4 Clamp Limits crossed (Max<Min) |
| **`Err_CV5Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV5 Clamp Limits crossed (Max<Min) |
| **`Err_CV6Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV6 Clamp Limits crossed (Max<Min) |
| **`Err_CV7Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV7 Clamp Limits crossed (Max<Min) |
| **`Err_CV8Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV8 Clamp Limits crossed (Max<Min) |
| **`P_Fanout`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 26 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Fanout`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_CV_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for display on HMI |
| **`Cfg_CV1_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 1 Engineering Units for display on HMI |
| **`Cfg_CV1_Label`** | `STRING_20` | `'Output 1'` | [Biến cấu hình thông số] Output 1 Label for display on HMI |
| **`Cfg_CV2_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 2 Engineering Units for display on HMI |
| **`Cfg_CV2_Label`** | `STRING_20` | `'Output 2'` | [Biến cấu hình thông số] Output 2 Label for display on HMI |
| **`Cfg_CV3_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 3 Engineering Units for display on HMI |
| **`Cfg_CV3_Label`** | `STRING_20` | `'Output 3'` | [Biến cấu hình thông số] Output 3 Label for display on HMI |
| **`Cfg_CV4_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 4 Engineering Units for display on HMI |
| **`Cfg_CV4_Label`** | `STRING_20` | `'Output 4'` | [Biến cấu hình thông số] Output 4 Label for display on HMI |
| **`Cfg_CV5_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 5 Engineering Units for display on HMI |
| **`Cfg_CV5_Label`** | `STRING_20` | `'Output 5'` | [Biến cấu hình thông số] Output 5 Label for display on HMI |
| **`Cfg_CV6_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 6 Engineering Units for display on HMI |
| **`Cfg_CV6_Label`** | `STRING_20` | `'Output 6'` | [Biến cấu hình thông số] Output 6 Label for display on HMI |
| **`Cfg_CV7_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 7 Engineering Units for display on HMI |
| **`Cfg_CV7_Label`** | `STRING_20` | `'Output 7'` | [Biến cấu hình thông số] Output 7 Label for display on HMI |
| **`Cfg_CV8_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Output 8 Engineering Units for display on HMI |
| **`Cfg_CV8_Label`** | `STRING_20` | `'Output 8'` | [Biến cấu hình thông số] Output 8 Label for display on HMI |
| **`Cfg_CVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tagname for destination of input CV Navigation button |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Fanout'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Fanout'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_NavTag`** | `STRING_NavTag[8]` | `{...}` | [Biến cấu hình thông số] Tagnames for destinations of Navigation buttons ([0]=output 1, ... [7]=output 8) |
| **`Cfg_Tag`** | `STRING_20` | `'P_Fanout'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Fanout'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
