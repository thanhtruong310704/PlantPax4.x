# PHẦN 0: TỔNG QUAN, MỤC LỤC CHI TIẾT & HƯỚNG DẪN SỬ DỤNG TÀI LIỆU
**Tài liệu gốc:** Rockwell Automation Publication `PROCES-RM013I-EN-P` (Tháng 04/2023) — Thay thế bản `PROCES-RM013H-EN-P` (Tháng 12/2021)  
**Tên đầy đủ:** *Rockwell Automation Library of Process Objects: Logic Instructions* (436 trang)  
**Tài liệu HMI đi kèm:** Publication `PROCES-RM014` (*Display Elements, Global Objects, and HMI Information*)

## 1. Tóm tắt thay đổi phiên bản (Summary of Changes - Trang 12)
* **Cập nhật khối lệnh AOI:** Nâng cấp hai khối lệnh cốt lõi là **`P_Intlk`** (Khóa liên động có First Out và Bypass) và **`P_Perm`** (Điều kiện cho phép khởi động có Bypass).
* **Cập nhật bảng tham số đính kèm:** Toàn bộ 5 tệp bảng tính Microsoft Excel đính kèm trong PDF đã được cập nhật để phản ánh các thay đổi về tham số (Parameters) và thẻ cấu hình cục bộ (Local Configuration Tags) của phiên bản thư viện **4.10.00 (4.1)**.

## 2. Hướng dẫn truy cập 5 tệp Excel đính kèm (Access the Attachments - Trang 12–13)
Bên trong tệp PDF `proces-rm013_-en-p.pdf` có đính kèm 5 tệp bảng tính Excel chứa danh sách đầy đủ toàn bộ tham số đầu vào, đầu ra và biến cục bộ của từng họ lệnh AOI (đã được trích xuất sẵn ra thư mục `/home/user/proces-rm013_vi_chi_tiet/attachments/` trên máy ảo):

| Tên tệp Excel đính kèm | Dung lượng | Nội dung chi tiết |
| :--- | :--- | :--- |
| `IO Processing 4.1.xlsx` | 168 KB | Toàn bộ tham số của 13 khối xử lý tín hiệu Vào/Ra (Chương 1) |
| `Regulatory_Procedural Control 4.1.xlsx` | 129 KB | Toàn bộ tham số của 7 khối điều khiển PID & Quy trình (Chương 2) |
| `Motors 4.1.xlsx` | 233 KB | Toàn bộ tham số của 17 khối Động cơ, Biến tần & Rơ-le quá tải (Chương 3) |
| `Valves 4.1.xlsx` | 129 KB | Toàn bộ tham số của 8 khối Van điều khiển & Thiết bị đa vị trí (Chương 4) |
| `Cross Functional 4.1.xlsx` | 45 KB | Toàn bộ tham số của 10 khối Liên động, Cảnh báo & Dùng chung (Chương 5) |

Mỗi tệp Excel bao gồm **3 trang tính (Tabs)** chuẩn hóa:
1. **Input Parameters:** Danh sách các tham số đầu vào (`Inp_*`, `Cfg_*`, `PCmd_*`, `OCmd_*`, `PSet_*`, `OSet_*`, `MSet_*`).
2. **Output Parameters:** Danh sách các tham số đầu ra (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`, `SrcQ_*`).
3. **Local Configuration Tags:** Các biến cấu hình nội bộ (`Cfg_*`, chuỗi ký tự `STRING` hiển thị HMI như `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`, `Cfg_EU`).

## 3. Hệ sinh thái tài liệu tham khảo PlantPAx (Additional Resources - Trang 14)

| Mã tài liệu | Tên tài liệu | Mô tả nội dung kỹ thuật |
| :--- | :--- | :--- |
| `PROCES-SG001` | *PlantPAx DCS Selection Guide* | Định nghĩa các thành phần hệ thống và quy tắc tính toán quy mô (Sizing) khi lựa chọn cấu hình hệ thống DCS PlantPAx. |
| `PROCES-UM001` | *PlantPAx DCS Infrastructure Configuration User Manual* | Quy trình cấu hình hạ tầng mạng, máy chủ, bộ điều khiển tạo nên hệ thống DCS hiện đại. |
| `PROCES-UM003` | *PlantPAx DCS Application Configuration User Manual* | Quy trình phát triển ứng dụng điều khiển và nhập các chiến lược mẫu (Process Strategies). |
| `PROCES-RM001` | *PlantPAx DCS Reference Manual* | Các khuyến nghị chuẩn hóa khi triển khai kiến trúc hệ thống PlantPAx. |
| `PROCES-RM013` & `PROCES-RM014` | *Library of Process Objects Reference Manuals* | Bộ đôi sổ tay tra cứu thuật toán logic AOI (`RM013`) và giao diện HMI/Faceplates (`RM014`). |
| `PROCES-RM003` | *Library of Logix Diagnostic Objects Reference Manual* | Các khối AOI chẩn đoán bộ điều khiển Logix (dung lượng bộ nhớ, thời gian quét CPU, lỗi truyền thông). |
| `PROCES-RM004` | *Library of Steam Table Instructions* | Các khối AOI tính toán bảng nhiệt động lực học hơi nước bão hòa và quá nhiệt (Steam Tables). |
| `1715-UM001` | *Redundant I/O System User Manual* | Hướng dẫn lắp đặt và cấu hình hệ thống I/O dự phòng nóng 1715 Redundant I/O. |

## 4. Cấu trúc bộ tài liệu dịch chi tiết trong thư mục `/home/user/proces-rm013_vi_chi_tiet/`
1. **`00_MUC_LUC_VA_TONG_QUAN.md`**: Tổng quan, lịch sử thay đổi, cấu trúc 5 file Excel đính kèm và tài liệu liên quan (Trang 1–14).
2. **`01_CHUONG_1_IO_PROCESSING.md`**: Dịch chi tiết 13 khối xử lý I/O (Trang 15–76).
3. **`02_CHUONG_2_REGULATORY_PROCEDURAL_CONTROL.md`**: Dịch chi tiết 7 khối điều khiển PID & Quy trình (Trang 77–118).
4. **`03_CHUONG_3_MOTORS_VA_BIEN_TAN.md`**: Dịch chi tiết 17 khối Động cơ, Biến tần PowerFlex, Khởi động mềm SMC & Rơ-le quá tải E300 (Trang 119–278).
5. **`04_CHUONG_4_VALVES.md`**: Dịch chi tiết 8 khối Van điều khiển, Van chống nhiễm chéo Mix-proof & Thiết bị đa vị trí (Trang 279–330).
6. **`05_CHUONG_5_CROSS_FUNCTIONAL.md`**: Dịch chi tiết 10 khối Liên động, Cho phép khởi động, Cảnh báo, Nguồn lệnh & Logic khả trình (Trang 331–396).
7. **`06_PHU_LUC_A_B_C.md`**: Dịch chi tiết Phụ lục A (Command Source & Simulation), Phụ lục B (41 khối LINT 64-bit & Date/Time) và Phụ lục C (>65 Process Strategies `.L5X`) (Trang 397–436).
8. **[`aoi_chi_tiet/00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`](./aoi_chi_tiet/00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)**: Cẩm nang giới thiệu chung, bảng phản xạ chọn nhanh khối AOI theo nhu cầu bài toán thực tế và mục lục liên kết trực tiếp tới toàn bộ 55 tệp tra cứu biến chi tiết trong thư mục `aoi_chi_tiet/`.
