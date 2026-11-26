# Báo cáo Day 13 — Phạm Anh Tuấn, 2A202603232

## 1. Kết quả sau cập nhật nhãn

Task `D13-2A202603232`, task/job 38 trên CVAT local 2.82.1. Bản xuất ngày 26/11/2026 được tải lại sau khi chỉnh nhãn; môi trường Windows và Chrome. Chưa có thông tin người kiểm chéo.

Bản mới có **126 cuboid dạng shape, 0 track**: 5 Bus, 98 Car, 8 Cyclist, 11 Pedestrian, 4 Truck.

Phiếu tự kiểm có **40/126 hộp mang cảnh báo** (31.7%). Có 2 hộp rỗng. SHA-256 trong phiếu khớp bảng A hiện tại. Cảnh báo là gợi ý rà soát, không tự động kết luận nhãn sai.

| Frame | Cuboid | Hộp có cảnh báo |
|---|---:|---:|
| s01f18 | 9 | 3 |
| s01f52 | 20 | 10 |
| s02f07 | 20 | 6 |
| s02f41 | 7 | 1 |
| s03f06 | 15 | 7 |
| s03f46 | 5 | 0 |
| s04f15 | 15 | 5 |
| s04f49 | 10 | 5 |
| s05f14 | 13 | 2 |
| s05f45 | 12 | 1 |

Ít hộp nhất: s03f46 (5 hộp/frame). Chưa thể suy ra ít vật hay bỏ sót chỉ từ số lượng nhãn.

Quy trình kiểm CVAT: đổi góc nhìn, xoay và dời cảnh; chỉnh Cuboid ở chế độ Shape và đối chiếu Top/Side/Front. Bật Cuboid orientation để kiểm hướng; kiểm hình chiếu trên camera. Việc chụp ảnh không thay thế nghiệm thu từng hộp.

## 2. Đọc frame và hệ tọa độ

Theo tài liệu repo: +x sang trái, +y ra sau, +z lên trên; phía trước xe là −y, mặt đường tham chiếu khoảng −1,7 m. Hai frame đầu có ảnh `t1-s01f18-3d.png`, `t1-s01f18-top.png`, `t1-s01f52-3d.png`, `t1-s01f52-top.png`. Ảnh Top tập trung vào cuboid đang chọn, chưa phải toàn cảnh BEV có chú thích trục.

| Frame | Class trong nhãn | Tâm xa nhất đã gán (m) |
|---|---|---:|
| s01f18 | Bus, Car | 62.08 |
| s01f52 | Car, Cyclist, Pedestrian | 50.75 |

Tám frame còn lại có ảnh `frame-<frame>-3d.png`, tương ứng các tên frame trong bảng mục 1. Chưa xác nhận độ dốc đường, vật bị bỏ sót và các cụm khó đọc. Camera hỗ trợ nhận diện diện mạo, màu và ngữ cảnh; LiDAR cung cấp vị trí theo mét, cao độ và phân bố điểm theo chiều sâu.

## 3. Ba cuboid đại diện

Các mẫu lấy từ bản xuất mới. `idx` là số thứ tự trong bảng A, có thể khác số ID hiển thị trên CVAT.

| Cuboid | Dài × rộng × cao (m) | Yaw (°) | Ground gap (m) | Cảnh báo |
|---|---|---:|---:|---|
| s01f18 #1 Car | 4.630 × 1.950 × 1.693 | 272.2 | 0.04 | Không có |
| s01f18 #4 Bus | 13.637 × 2.756 × 3.158 | 272.0 | 0.09 | Không có |
| s01f52 #11 Pedestrian | 0.650 × 0.650 × 1.720 | 0.0 | 0.17 | Không có |

Các mẫu chưa được xác nhận đầy đủ bằng cả Top/Side/Front và camera. Khi so bảng 4.3 cần phân biệt sedan/SUV, người lớn/trẻ em và tư thế. Không có cảnh báo không đồng nghĩa nhãn đúng. Chưa có biên bản hiệu chuẩn với lớp hoặc danh sách vật chủ động bỏ qua.

## 4. Hiện tượng chất lượng

| Hiện tượng | Giả thuyết và giới hạn kiểm chứng |
|---|---|
| Bóng che | Vật gần có thể che vật xa; cần đối chiếu vùng thiếu điểm với camera |
| Thưa ở xa | Số điểm phụ thuộc khoảng cách, kích thước và che khuất; xem mẫu ở mục 5 |
| Phản xạ thấp | Hộp ít điểm chưa đủ chứng minh phản xạ thấp; phải loại trừ hộp sai vị trí |
| Điểm lạ | Có thể là nhiễu hoặc điểm của vật khác; không mở hộp chỉ để bao điểm rời |

Chưa có bốn ảnh chuyên biệt xác nhận các hiện tượng trên. Không dùng tên hiện tượng để đặt cho ảnh chưa được kiểm chứng.

## 5. Số liệu và tự kiểm

`dist_m = √(x²+y²)`, `r = √(x²+y²+z²)`, `z đáy = z − h/2`; số được tính từ bảng A đã làm tròn.

| Cuboid | z (m) | h (m) | z đáy (m) | dist_m (m) | r (m) | yaw (°) |
|---|---:|---:|---:|---:|---:|---:|
| s01f18 #1 Car | -0.760 | 1.693 | -1.606 | 25.80 | 25.810 | 272.2 |
| s01f18 #4 Bus | -0.397 | 3.158 | -1.976 | 27.84 | 27.839 | 272.0 |
| s01f52 #11 Pedestrian | -0.326 | 1.720 | -1.186 | 13.68 | 13.686 | 0.0 |

Gần nhất: s03f06 #5 Car, dist_m = 3.13 m, r = 3.128 m.

Xa nhất: s04f49 #10 Bus, dist_m = 108.81 m, r = 108.813 m.

Ground gap trong phiếu dùng mặt đường cục bộ; đáy gần −1,7 m chỉ phù hợp mốc tham chiếu. Chưa xác nhận cặp xe cùng/ngược chiều bằng camera.

| Car | dist_m (m) | points_in |
|---|---:|---:|
| s03f46 #4 Car | 4.58 | 2556 |
| s03f06 #4 Car | 15.00 | 715 |
| s02f07 #3 Car | 25.45 | 200 |
| s05f45 #2 Car | 40.94 | 119 |

Số điểm trong hộp chưa chuẩn hóa theo kích thước và che khuất. Mốc tham khảo bài học cho Car là 1968/718/214/72 điểm ở khoảng 0–10/10–20/20–30/30–40 m; không dùng để tự động loại nhãn.

Các cảnh báo cần rà soát trong bản mới:

| Cuboid | Cảnh báo |
|---|---|
| s01f18 #7 Car | length=1.59 ngoài khoảng 3.0–6.0 của Car; width=0.90 ngoài khoảng 1.4–2.3 của Car; height=1.04 ngoài khoảng 1.2–2.3 của Car |
| s01f18 #8 Car | length=1.38 ngoài khoảng 3.0–6.0 của Car; width=4.05 ngoài khoảng 1.4–2.3 của Car; length < width: mũi tên yaw có dọc thân xe không? |
| s01f18 #9 Car | length=1.03 ngoài khoảng 3.0–6.0 của Car; width=4.70 ngoài khoảng 1.4–2.3 của Car; length < width: mũi tên yaw có dọc thân xe không? |
| s01f52 #1 Car | chồng lên hộp 13 (mỗi vật 1 hộp) |
| s01f52 #2 Car | 71 điểm sát ngay ngoài mép (quá chật, hay vật bên cạnh?) |
| s01f52 #6 Car | đáy thấp hơn mặt đường 0.27 m (chìm?); chồng lên hộp 14 (mỗi vật 1 hộp) |

Bản trước có 198 hộp và 117 hộp cảnh báo; bản mới có 126 hộp và 40 hộp cảnh báo. Vì có thêm/xóa/đổi nhãn, idx có thể đổi; không suy diễn thao tác sửa từng vật chỉ từ hai tổng số. Chưa có biên bản kiểm chéo.

## 6. Ý nghĩa nhãn và giới hạn

Nhãn 3D dùng mét vì biểu diễn vị trí và kích thước trong không gian cảm biến. Vật thưa điểm cần quy tắc về độ chắc chắn để tránh biến suy đoán thiếu bằng chứng thành nhãn huấn luyện.

Nguồn: `submission/D13-2A202603232-bangA.csv`, `submission/D13-2A202603232-tukiem.csv` và bản JSON API lưu local tại `work/cvat-export/D13-2A202603232-task38.json`.

Đã chụp lại 10 frame cùng hai góc Top của hai frame đầu, đóng gói trong `work/K5-L23-DAY13-2A202603232-images.zip`. Bản dữ liệu nhãn và hai bảng được nén riêng thành `work/D13-2A202603232-labeled-data.zip`. Ảnh và ZIP giữ local, không đưa lên repo công khai.

Bộ ảnh hiện ghi nhận trạng thái nhãn mới; chưa thay thế đủ 11 ảnh chuyên biệt theo SUBMISSION.md (còn ba ảnh mẫu và bốn ảnh hiện tượng). Chưa hoàn tất kiểm chéo hoặc nghiệm thu toàn bộ hộp.
