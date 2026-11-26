# Báo cáo Day 13 — Phạm Anh Tuấn, 2A202603232

## 1. Kết quả và phạm vi kiểm chứng

Task `D13-2A202603232`, task/job 38, CVAT local 2.82.1; xuất ngày 26/11/2026 trên Windows qua Chrome. Chưa ghi nhận phiên bản Chrome và MSSV người kiểm chéo.

Bản xuất có **198 cuboid dạng shape, 0 track**: 175 Car, 9 Bus, 14 Pedestrian. Tất cả có metadata `source=manual`; trường này không chứng minh nhãn được vẽ thủ công. Nhãn có pipeline AI hỗ trợ, chưa được nghiệm thu từng hộp.

Phiếu tự kiểm khớp SHA-256 của bảng A, có **117/198 hộp mang cảnh báo (59,1%)**, gồm 27 hộp rỗng. Tính nhất quán của bảng đã được kiểm; class, yaw, chồng nhãn và hộp rỗng còn cần đối chiếu ảnh.

| Frame | Cuboid | Có cảnh báo |
|---|---:|---:|
| s01f18 | 9 | 2 |
| s01f52 | 18 | 11 |
| s02f07 | 32 | 20 |
| s02f41 | 21 | 12 |
| s03f06 | 23 | 16 |
| s03f46 | 20 | 16 |
| s04f15 | 23 | 11 |
| s04f49 | 19 | 13 |
| s05f14 | 20 | 10 |
| s05f45 | 13 | 6 |

s01f18 ít nhất với 9 hộp. Chưa thể phân biệt cảnh ít vật với bỏ sót chỉ từ số lượng nhãn.

Quy trình kiểm CVAT: xoay, dời, chuyển góc nhìn từ trên xuống để đọc cảnh. Chọn Cuboid ở chế độ Shape và kiểm Top/Side/Front khi chỉnh hộp. Bật Cuboid orientation để kiểm yaw, dùng preview với calibration VF để đối chiếu camera. Đây là quy trình nghiệm thu, không phải xác nhận đã kiểm mọi hộp.

## 2. Đọc frame và hệ tọa độ

Theo `docs/COORDINATES.md`: +x sang trái, +y ra sau, +z lên trên; phía trước là −y. Mặt đường tham chiếu khoảng −1,7 m. Không suy ra cạnh của ảnh Top khi chưa biết góc nhìn.

| Hai frame đầu | Class trong nhãn | Khoảng cách tâm xa nhất đã gán |
|---|---|---:|
| s01f18 | Bus, Car | 49,64 m |
| s01f52 | Car, Pedestrian | 48,10 m |

Các con số trên lấy từ bảng A, không khẳng định đó là vật xa nhất có thể nhận biết trong cảnh. Chưa có ảnh 3D/Top để xác nhận độ dốc đường, vật phía sau/hai bên và cụm khó đọc. Tám frame còn lại được thống kê ở mục 1; chưa có bằng chứng đọc cảnh thủ công.

Camera hỗ trợ phân biệt diện mạo, màu sắc và ngữ cảnh của vật. LiDAR cung cấp vị trí theo mét, cấu trúc cao độ và phân bố điểm theo chiều sâu. Đây là đặc tính cảm biến, chưa phải các quan sát riêng đã xác nhận trên hai frame.

## 3. Ba cuboid đại diện

| Cuboid | Dài × rộng × cao (m) | Yaw (°) | Ground gap (m) | Cảnh báo |
|---|---|---:|---:|---|
| s01f18 #1 Car | 4,626 × 1,968 × 1,645 | 269,1 | 0,04 | Chồng hộp #6 |
| s01f18 #5 Bus | 13,637 × 2,756 × 3,158 | 272,0 | 0,09 | Không có |
| s01f52 #15 Pedestrian | 0,650 × 0,650 × 1,145 | 0,0 | 0,14 | Không có |

Mẫu được lấy nguyên từ nhãn đã tải, chưa được xác nhận bằng Top/Side/Front và camera. Không có cảnh báo không đồng nghĩa nhãn đúng.

Theo bảng 4.3 trong guideline, Car mẫu rộng và cao vượt khoảng sedan (rộng 1,70–1,90 m; cao 1,40–1,60 m); cần phân biệt sedan với SUV/van trước khi kết luận. Pedestrian mẫu thấp hơn người lớn (1,50–1,95 m), trong khi dài/rộng vượt khoảng trẻ em (0,3–0,6 m); cần kiểm tư thế và class.

Chưa có biên bản hiệu chuẩn với lớp, kiểm chéo hoặc danh sách vật chủ động bỏ qua. Không thể kết luận không có vật bỏ sót từ bản xuất này.

## 4. Bốn hiện tượng chất lượng

| Hiện tượng | Bằng chứng và giới hạn | Giả thuyết cần kiểm |
|---|---|---|
| Bóng che | Chưa có ảnh minh chứng | Vật gần che vật xa; kiểm vùng thiếu điểm cùng camera |
| Thưa ở xa | Số điểm tại mục 5 | Khoảng cách làm giảm mật độ; cũng chịu ảnh hưởng của kích thước, che khuất và sai hộp |
| Phản xạ thấp | 27 hộp rỗng chưa đủ xác định nguyên nhân | Phản xạ thấp, sai vị trí hoặc nhãn vật không tồn tại đều cần được xem xét |
| Điểm lạ | Chưa có ảnh xác nhận outlier | Nhiễu hoặc điểm của vật khác; không mở hộp chỉ để bao mọi điểm rời |

Các nguyên nhân trên là giả thuyết, không phải kết luận quan sát đã kiểm chứng.

## 5. Số liệu cuboid và tự kiểm

`dist_m = √(x²+y²)`, `r = √(x²+y²+z²)`, `z đáy = z − h/2`. Số lấy từ bảng A, có sai số làm tròn.

| Cuboid | z (m) | h (m) | z đáy (m) | dist_m (m) | r (m) | yaw (°) |
|---|---:|---:|---:|---:|---:|---:|
| s01f18 #1 Car | -0,881 | 1,645 | -1,703 | 11,27 | 11,302 | 269,1 |
| s01f18 #5 Bus | -0,397 | 3,158 | -1,976 | 27,84 | 27,839 | 272,0 |
| s01f52 #15 Pedestrian | -0,727 | 1,145 | -1,299 | 9,74 | 9,766 | 0,0 |

Gần nhất: s04f15 #1 Car, dist_m = 1,52 m, r = 1,696 m. Xa nhất: s04f49 #18 Car, dist_m = 49,85 m, r = 49,863 m. Do làm tròn các cột độc lập, r tính lại có thể thấp hơn dist_m đã làm tròn vài milimét.

Đáy gần −1,7 m chỉ phù hợp mặt đường tham chiếu; ground gap trong phiếu tự kiểm dùng ước lượng đường cục bộ. Chưa xác nhận bằng mắt hộp chạm đường. Car mẫu có yaw gần 270° nhưng tâm y dương nên nằm phía sau; không được gọi là xe cùng chiều phía trước chỉ dựa vào yaw. Chưa có cặp xe cùng/ngược chiều được xác nhận trên camera.

| Car | dist_m (m) | points_in |
|---|---:|---:|
| s03f06 #16 | 5,31 | 448 |
| s03f06 #6 | 14,97 | 603 |
| s02f07 #9 | 25,45 | 200 |
| s02f07 #19 | 40,55 | 21 |

Trong hai mẫu cuối, khoảng cách tăng khoảng 1,59 lần còn số điểm giảm còn 10,5%. Bốn mẫu không giảm đơn điệu: hộp 14,97 m có nhiều điểm hơn hộp 5,31 m. Không thể coi khoảng cách là nguyên nhân duy nhất. Mốc bài học cho Car là khoảng 1968/718/214/72 điểm ở các khoảng 0–10/10–20/20–30/30–40 m, chỉ để tham khảo vì diện tích và che khuất khác nhau.

s01f18 #1 bị cảnh báo chồng #6; cần kiểm có cùng một vật hay không. Lần xuất phục vụ báo cáo không sửa annotation trên CVAT. Chưa có bằng chứng hai cuboid đã chỉnh sau preview hoặc đối chiếu số liệu với bạn kiểm chéo. Giữ cảnh báo để rà soát, không coi là đã chấp nhận nhãn.

## 6. Ý nghĩa nhãn, AI và giới hạn

Nhãn 3D dùng mét vì biểu diễn vị trí và kích thước trong không gian cảm biến, không phụ thuộc riêng vào độ phân giải ảnh. Vật ít điểm cần quy tắc về độ chắc chắn để tránh biến suy đoán thiếu bằng chứng thành nhãn huấn luyện.

- Công cụ: pipeline AI hỗ trợ gán cuboid; Codex hỗ trợ xuất dữ liệu, kiểm bảng và soạn báo cáo.
- Yêu cầu chính: tải nhãn task cá nhân, viết báo cáo theo repo, commit và push.
- Cách dùng: giữ số liệu thật và cảnh báo, công khai phần chưa kiểm thủ công; không coi metadata `manual` là bằng chứng không dùng AI.

Nguồn: `submission/D13-2A202603232-bangA.csv`, `submission/D13-2A202603232-tukiem.csv` và tài liệu guideline/tọa độ của repo. JSON API lưu local tại `work/cvat-export/D13-2A202603232-task38.json`, không đưa lên repo công khai.

Báo cáo phản ánh số liệu và tình trạng chất lượng hiện tại. Còn thiếu 11 ảnh minh chứng, ZIP ảnh LMS và kiểm chéo; chưa đủ cơ sở khẳng định hoàn tất toàn bộ yêu cầu thực hành.
