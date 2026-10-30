# Thuật toán phân cụm BIRCH

Mã nguồn và kết quả thực nghiệm nằm trong **Do-An-Birch.ipynb**. Notebook có
lý thuyết cốt lõi, công thức CF, ví dụ tính tay và giải thích ngắn bên cạnh code.
BIRCH và weighted K-Means được tự cài đặt; tiền xử lý dùng `StandardScaler`,
PCA và các chỉ số đánh giá dùng scikit-learn, biểu đồ dùng Matplotlib.

Bài báo cáo **Bao_cao_BIRCH.docx** được lưu riêng trong Documents, trình bày lý thuyết, tính toán bằng tay,
giải thích cài đặt, kết quả 2D/8D và so sánh các thuật toán.

## Chạy notebook

Dùng Python 3.10 trở lên. Chạy các lệnh sau tại thư mục gốc của repository:

```powershell
python -m pip install -r requirements.txt
python -m jupyterlab
```

Mở `Do-An-Birch.ipynb`, chọn kernel có đủ thư viện rồi chọn
**Restart Kernel and Run All**. Có thể mở bằng VS Code và chọn môi trường Python
vừa cài thư viện. Notebook chạy trên CPU, không cần dịch vụ bên ngoài.

## Các file

```text
Do-An-ML/
  Do-An-Birch.ipynb
  data/
    khach_hang_2d.csv
    khach_hang_8d.csv
    minh_hoa_birch.csv
    data_dictionary.md
  outputs/
    benchmark_birch_full.csv
    benchmark_cung_tap_con.csv
    (các hình PNG dùng trong báo cáo)
  README.md
  requirements.txt
  .gitignore
```

Ba CSV lần lượt có 30.000, 20.000 và 20 dòng. Dữ liệu khách hàng được mô phỏng,
không dùng dữ liệu cá nhân thật. Notebook đọc trực tiếp ba CSV đã có, không sinh
hoặc ghi đè dữ liệu đầu vào khi Run All.
Xem [từ điển dữ liệu](data/data_dictionary.md) để biết đơn vị và quy luật sinh.

## Cài đặt và đánh giá

BIRCH tự viết gồm ClusteringFeature, CFEntry, CFNode, CFTree và Birch. CF lưu N,
vector LS và scalar SS; threshold là bán kính RMS sau gộp. Chương trình xử lý
hấp thụ điểm, tạo entry, tách lá/nút trong/gốc và cập nhật CF cha. Bước cuối
là weighted K-Means tự viết với trọng số N, không gọi thuật toán clustering có sẵn.

Ví dụ nhỏ dùng giá trị thô để tính tay và vẽ trạng thái cây. Dữ liệu lớn dùng
Z-score bằng `StandardScaler` của scikit-learn. PCA chỉ để vẽ kết quả 8D, không dùng thay dữ liệu phân cụm.

Hai bản BIRCH được so trên cùng tập dữ liệu đầy đủ. K-Means, DBSCAN và
Agglomerative được so trên cùng 3.000 dòng cố định cho mỗi dataset. Thời gian
là trung vị ba lần xây mô hình và lấy nhãn, một thread. Silhouette dùng cùng mẫu
cố định tối đa 1.200 điểm; bỏ nhiễu DBSCAN khi tính chỉ số này. ARI/NMI vẫn gồm
nhãn nhiễu. Bảng, hình và nhận xét có số liệu được sinh khi chạy notebook.

## Giới hạn

Chưa cài tái xây cây theo ngân sách bộ nhớ, partial_fit hoặc refinement lặp;
không tuyên bố đủ bốn pha BIRCH nguyên bản. Dữ liệu giả lập, tham số cố định,
chưa tối ưu cho từng phương pháp. Hai bản BIRCH khác bước gom cụm cuối nên
không yêu cầu kết quả trùng nhau. Chưa đo peak RAM đáng tin cậy.

Lần kiểm tra ngày 21/10/2026 dùng Python 3.13.5 và scikit-learn 1.9.0.
Toàn bộ 19 cell code đã chạy từ kernel sạch, không lỗi; thời gian thực hiện
khoảng 59 giây trên máy kiểm tra. Các bảng benchmark trong báo cáo dùng kết quả
của lần chạy này.
