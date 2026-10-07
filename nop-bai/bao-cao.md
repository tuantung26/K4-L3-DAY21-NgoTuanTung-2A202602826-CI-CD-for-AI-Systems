# Báo Cáo Lab Day 21 - CI/CD cho AI Systems


| | |
|---|---|
| Họ và tên | Ngo Tuan Tung |
| MSSV | 2A202602826 |
| Lớp / Khóa | K4 |
| Repo GitHub | https://github.com/tuantung26/K4-L3-DAY21-NgoTuanTung-2A202602826-CI-CD-for-AI-Systems |
| Ngày nộp | 07/10/2026 |

---

## 1. Bộ Siêu Tham Số Đã Chọn và Lý Do


| Lần chạy | n_estimators | learning_rate | max_depth | f1_score | accuracy |
|---|---|---|---|---|---|
| 1 | 200 | 0.1 | 5 | 0.7149 | 0.874 |
| 2 | 100 | 0.1 | 3 | 0.7109 | 0.878 |
| 3 | 50 | 0.05 | 2 | 0.6051 | 0.846 |

**Bộ siêu tham số đã chọn:** `n_estimators=200`, `learning_rate=0.1`, `max_depth=5`.

**Lý do:** Lần chạy số 1 có F1-score (0.7149) cao nhất nên được chọn làm mô hình tốt nhất, mặc dù accuracy của lần chạy số 2 (0.878) lại nhỉnh hơn một chút. Trong bài toán dữ liệu mất cân bằng (imbalanced data) như dự đoán thu nhập, việc ưu tiên F1-score để giảm thiểu sai sót cho lớp thiểu số (thu nhập >50K) quan trọng hơn việc tối đa hoá accuracy. Việc tăng max_depth và n_estimators đã giúp mô hình học thêm nhiều đặc trưng phức tạp, cải thiện F1 đáng kể so với việc giảm chúng.


---

## 2. Vì Sao Ngưỡng Chất Lượng Đặt Trên F1 Chứ Không Phải Accuracy

Tập dữ liệu Adult mất cân bằng, trong đó số lượng mẫu có "thu nhập thấp" chiếm đa số (khoảng 75%). Nếu một mô hình dự đoán "thu nhập thấp" cho tất cả các mẫu, nó vẫn đạt accuracy khoảng 75% mặc dù hoàn toàn vô dụng. F1-score (của lớp thiểu số - thu nhập cao) kết hợp cả Precision và Recall, đo lường chính xác khả năng mô hình phát hiện đúng những trường hợp cần quan tâm mà không bị "thao túng" bởi lớp đa số. Chúng ta không dùng average="weighted" vì nó sẽ bị chi phối bởi độ chính xác của lớp thu nhập thấp, làm mờ đi hiệu suất thực sự trên lớp thu nhập cao. Do đó đặt ngưỡng ở F1 là thước đo thiết thực và khó để "lách" nhất.


---

## 3. Khó Khăn Gặp Phải và Cách Giải Quyết


| Khó khăn | Nguyên nhân | Cách giải quyết |
|---|---|---|
| Lỗi SSH khi deploy lên EC2 | Máy ảo trắng, chưa thiết lập systemd service | Tạo file cấu hình systemd `income-api.service` và bật chạy nền. |
| Lỗi load file model.joblib trên server | Lệch phiên bản thư viện scikit-learn giữa CI/CD (1.4.2) và máy ảo (1.6.1) | Kết nối vào EC2, hạ cấp cài đúng bản scikit-learn==1.4.2 bằng pip. |
| Lệnh git không chạy được trong PowerShell | PowerShell trên máy Windows không được thiết lập biến môi trường trỏ đến thư mục Git | Chuyển sang sử dụng trực tiếp giao diện GitHub Desktop để tạo commit. |

---

## 4. So Sánh Bước 2 và Bước 3 (bắt buộc, 2 - 3 câu)


| | f1_score | accuracy |
|---|---|---|
| Bước 2 (chỉ `train_batch1`) | 0.7149 | 0.874 |
| Bước 3 (thêm `train_batch2`) | 0.7354 | 0.882 |

**Nhận xét:** Điểm F1-score và Accuracy đều tăng lên đáng kể khi bổ sung dữ liệu mới. Điều này chứng tỏ phần dữ liệu `train_batch2` được thêm vào mang lại thông tin hữu ích giúp mô hình học được các mẫu đặc trưng tổng quát và chính xác hơn, làm giảm sai số phân loại cho lớp thu nhập cao.


---

## 5. Phần Bonus Đã Thực Hiện (nếu có)


- [ ] Bonus 1 - Tracking MLflow từ xa với DagsHub: ___
- [ ] Bonus 2 - Điều chỉnh ngưỡng quyết định: ___
- [ ] Bonus 3 - Báo cáo precision / recall tự động: ___
- [ ] Bonus 4 - Hoàn trả về phiên bản trước: ___
- [ ] Bonus 5 - Cảnh báo lệch lạc dữ liệu: ___
