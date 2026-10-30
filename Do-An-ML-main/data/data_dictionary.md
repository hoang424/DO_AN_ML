# Từ điển dữ liệu mô phỏng

Các bản ghi mô tả khách hàng của cửa hàng bán lẻ trực tuyến giả định. “Tháng” là
cửa sổ 30 ngày gần nhất. Tiền tệ tính theo **nghìn VND**, không phải VND.
Không có dữ liệu khách hàng thật. Seed 42 cho 2D, 43 cho 8D.

| Cột | Ý nghĩa và đơn vị | Kiểu | Phạm vi / quy tắc |
|---|---|---|---|
| ma_khach_hang | Mã giả, duy nhất trong từng CSV | string | A000001… / B000001… / MH01…; metadata |
| khu_vuc | Khu vực giả định | string | Bac, Trung, Nam; chỉ có trong 8D, không đưa vào clustering |
| so_don_hang_thang | Số đơn đã thanh toán trong 30 ngày | int | 0–50 trong dữ liệu lớn; 1–18 ở ví dụ nhỏ |
| tong_chi_tieu_thang_nghin_vnd | Tổng giá trị đơn đã thanh toán, trước hoàn trả | float | >=0; <=150.000 nghìn VND |
| gia_tri_don_trung_binh_nghin_vnd | Giá trị đơn trung bình, trước hoàn trả | float | 40–3.000 khi có đơn; quy ước 0 khi không có đơn |
| so_ngay_tu_lan_mua_cuoi | Recency tại ngày chốt dữ liệu | int | 0–29 nếu có đơn tháng này; 31–365 nếu không có đơn; 2–12 trong ví dụ nhỏ |
| so_luot_xem_san_pham_thang | Số lượt xem, có thể xem nhiều lần một sản phẩm | int | >=0; số lượt xem ít nhất 2 lần số đơn, không có trần cố định |
| ty_le_don_dung_khuyen_mai | Số đơn có khuyến mãi / số đơn tháng | float | [0,1]; 0 khi không có đơn, không phải tỷ lệ giảm giá |
| so_nhom_hang_da_mua | Số nhóm hàng khác nhau đã mua trong 6 tháng | int | 0–12; không vượt số đơn 6 tháng trong quá trình sinh |
| so_don_hoan_tra_6_thang | Số đơn có hoàn trả trong 6 tháng | int | >=0, không vượt số đơn 6 tháng trong quá trình sinh |
| nhom_khach_hang_mo_phong | Quy luật sinh của khách hàng | string | G1, G2, G3, G4, G5, GX |

`khach_hang_2d.csv`: 30.000 dòng, mã + hai đặc trưng đầu + nhãn.
`khach_hang_8d.csv`: 20.000 dòng, mã + khu vực + tám đặc trưng + nhãn.
Hai CSV lớn được sinh độc lập, không phải hai phép chiếu của cùng một tập khách hàng.
`minh_hoa_birch.csv`: 20 dòng, mã + `so_don_hang_thang` +
`so_ngay_tu_lan_mua_cuoi`. Các cặp số nguyên nhỏ được chọn để tính tay và gây tách
cây với threshold=1, branching_factor=2. Ví dụ dùng giá trị thô với đơn vị khác
nhau có chủ ý; không dùng thiết lập này cho benchmark kinh doanh.

## Quy luật sinh và ý nghĩa nhãn

Dữ liệu được chuẩn bị theo năm cấu hình mô phỏng, ký hiệu G1–G5. Mỗi cấu
hình xác định phân phối xác suất của các đặc trưng, không phải một phân loại
khách hàng được suy ra từ thực tế. Bảng sau ghi thông số mục tiêu khi mô phỏng:

| Nhãn | Số đơn kỳ vọng / 30 ngày | Trung vị giá trị đơn (nghìn VND) |
|---|---:|---:|
| G1 | 0,8 | 220 |
| G2 | 3 | 330 |
| G3 | 4,5 | 240 |
| G4 | 10 | 420 |
| G5 | 6 | 1.000 |

“Số đơn kỳ vọng” là trung bình của phân phối dùng để lấy mẫu số đơn tháng.
“Trung vị giá trị đơn” là tham số của phân phối giá trị đơn trước khi quy ước
bằng 0 cho khách không phát sinh đơn. Đây là thông số mô phỏng, nên trung bình
và trung vị tính từ CSV có thể khác bảng. G3 còn được đặt xác suất sử dụng
khuyến mãi cao hơn các cấu hình còn lại; điều này không đồng nghĩa mọi khách
G3 đều có tỷ lệ khuyến mãi cao. G2 chỉ là một cấu hình trong thí nghiệm,
không đại diện cho khách hàng trung bình của thị trường.

**Sự chồng lấn** nghĩa là các cấu hình có thể sinh ra các giá trị đặc trưng
bằng hoặc gần nhau. Ví dụ minh họa: hai khách đều có 4 đơn, chi 1.200 nghìn
VND trong tháng, nhưng có tỷ lệ đơn dùng khuyến mãi lần lượt là 0,25 và 1.
Hai điểm trùng vị trí trong 2D dù khác nhau ở đặc trưng khuyến mãi. Ví dụ này
giải thích giới hạn của hai đặc trưng, không phải một cặp quan sát trích từ CSV.

Khoảng 1% bản ghi được lấy mẫu trên miền số đơn và giá trị đơn rộng hơn,
gắn mã **GX**. Đây là mẫu bổ sung để quan sát ảnh hưởng của các giá trị phân tán;
không mặc định mọi điểm GX là ngoại lệ thống kê hay thuộc cùng một cụm.
Tất cả các bản ghi này vẫn được giữ khi phân cụm và đánh giá.

Cột `nhom_khach_hang_mo_phong` ghi cấu hình dùng khi chuẩn bị mỗi bản ghi.
Cột này chỉ phục vụ đánh giá ARI/NMI, không được đưa vào ma trận đặc trưng.
Nhãn dự đoán 0–4 là mã cụm riêng của thuật toán, không tương ứng trực tiếp
với G1–G5. K=5 là lựa chọn thí nghiệm dựa trên thiết kế dữ liệu, chưa phải
bằng chứng dữ liệu có đúng năm cụm tự nhiên.

Tỷ lệ chọn G1–G5 trước bước bổ sung GX lần lượt là 38%, 26%, 18%, 12%, 6%.
Số đơn dùng mô hình Gamma–Poisson; giá trị đơn dùng phân phối lognormal.

Tổng chi tiêu = số đơn × giá trị đơn trung bình (sai số làm tròn <=0,01 nghìn VND).
Số đơn 6 tháng là biến nội bộ, bằng số đơn tháng + số đơn của 5 tháng trước;
hoàn trả được lấy mẫu binomial từ số đơn đó. Chi tiêu tháng không trừ hoàn trả
để không nhầm đơn phát sinh ở tháng khác. Không suy ra số đơn 6 tháng từ số đơn
tháng hiện tại bằng cách nhân cố định với 6.

Mã khách hàng, khu vực và nhãn mô phỏng không nằm trong ma trận đặc trưng.

Notebook đọc các CSV có sẵn trong thư mục này; phần mô tả quy luật sinh ở trên
giải thích nguồn gốc dữ liệu đã chuẩn bị, không phải code được chạy lại khi Run All.
