# Báo cáo Day 13 — Phạm Anh Tuấn, 2A202603232

## 1. Kết quả sau cập nhật nhãn

Task `D13-2A202603232`, task/job 38 trên CVAT local 2.82.1. Bản xuất ngày 26/11/2026 được tải lại sau khi chỉnh nhãn; môi trường Windows và Chrome 158.0.0.0. Chưa có thông tin người kiểm chéo.

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

Theo hệ VF: +x sang trái, +y ra sau, +z lên trên. Hai ảnh `t1-s01f18-top.png` và `t1-s01f52-top.png` đã chụp toàn cảnh từ trên xuống và chú thích: phía trước (−y) ở trên, +x bên trái, +y ở dưới. Ảnh 3D tương ứng: `t1-s01f18-3d.png`, `t1-s01f52-3d.png`.

| Frame | Mặt đường và bố cục | Tầm xa trong bản nhãn | Phía sau/hai bên; vùng khó đọc |
|---|---|---|---|
| s01f18 | Đường nhiều làn, vùng gần tương đối phẳng; LiDAR cao hơn đường khoảng 1,7 m theo mốc tài liệu. Xe con trắng gần bên trái và xe buýt phía trước bên trái | Car #6 ở 47,01 m còn 49 điểm; hộp #7 ở 62,08 m đã vượt phạm vi 50 m của bài | Camera sau có nhiều xe con; vùng khuất xa hơn xe buýt ít điểm, không thể coi là đường trống |
| s01f52 | Hàng xe phía trước; cây, vỉa hè và lối xe hai bánh bên phải. Không thấy thay đổi cao độ lớn trong vùng đường gần | Tâm hộp xa nhất 50,75 m, sát nhưng vượt giới hạn bài | Có xe con phía sau, người và xe hai bánh phía phải; cụm quanh gốc cây dễ lẫn với người nếu chỉ đọc LiDAR |

Ba chi tiết camera làm rõ trong hai frame: màu trắng/đen của xe, hình dáng xe buýt so với xe con, và người đang ngồi trên xe hai bánh ở lối bên phải. Ba thông tin hình học từ LiDAR: khoảng cách xe gần 6,59 m, phân bố điểm và vùng mất điểm sau xe buýt, cao độ/khoảng hở đáy hộp so với mặt đường. Camera không tự cung cấp trực tiếp các số đo theo mét này.

Tám frame lướt qua camera và BEV, ảnh tổng quan tên `frame-<frame>-3d.png`:

| Frame | Quan sát |
|---|---|
| s02f07 | Giao lộ rộng; xe buýt bên trái, người và xe hai bánh ở phía phải; nhiều vật giao nhau trong ảnh chiếu |
| s02f41 | Đường có hàng rào phân cách; xe con phía trước và phía sau, người ở vỉa hè phải |
| s03f06 | Xe sát hai bên và tường quây công trình; vật gần có thể che vùng phía sau |
| s03f46 | Nhãn hiện có 5 xe con; camera bên phải còn thấy người đi xe hai bánh chưa có hộp trong preview, nên số ít không chứng minh cảnh chỉ có 5 vật |
| s04f15 | Nhiều xe ở cả phía trước và sau; một hộp sau xe thu dữ liệu không chứa điểm, cần kiểm lại |
| s04f49 | Xe con và xe hai bánh gần hàng rào; hộp Bus ở 108,81 m vượt xa phạm vi bài |
| s05f14 | Dải cây phân cách che phần thấp của xe ở phía trái; đối chiếu ảnh khi xét đáy hộp |
| s05f45 | Giao lộ, xe van phía trước và xe tải hai bên; hướng xe không đồng nhất với hướng xe thu dữ liệu |

## 3. Ba cuboid đại diện

`idx` là số thứ tự trong bảng A, có thể khác ID hiển thị trên CVAT. Ba ảnh dưới đây chụp vật đang chọn cùng các cửa sổ Top/Side/Front.

| Ảnh | Cuboid | Dài × rộng × cao (m) | Yaw (°) | Ground gap (m) |
|---|---|---|---:|---:|
| `t2-a-car.png` | s01f18 #2 Car gần | 4,742 × 1,968 × 1,473 | 268,5 | 0,04 |
| `t2-b-bus-truck.png` | s01f18 #4 Bus | 13,637 × 2,756 × 3,158 | 272,0 | 0,09 |
| `t2-c-ped-cyclist.png` | s01f52 #11 Pedestrian | 0,650 × 0,650 × 1,720 | 0,0 | 0,17 |

Car gần có 2146 điểm, hình chiếu khớp vị trí xe trắng phía trước bên trái; yaw gần 270° phù hợp hướng dọc làn. Bus có 953 điểm, khối dài và hình chiếu tương ứng xe buýt. Pedestrian có 147 điểm và khối đứng hẹp; hướng mặt/đi chưa đủ rõ để nghiệm thu yaw 0°. Ba ground gap đều dưới 0,3 m, nhưng đây là kiểm số với đường cục bộ, không khẳng định mọi mặt hộp đều đạt dung sai.

So bảng 4.3: Pedestrian nằm trong khoảng người lớn (dài 0,4–0,9 m; rộng 0,5–0,9 m; cao 1,50–1,95 m). Car có dài/cao trong khoảng sedan, rộng 1,968 m vượt mốc 1,90 m khoảng 3,6%; cần phân biệt loại xe và kiểm mép ngang, không tự đổi class chỉ vì vượt mốc tham khảo. Bus dài 13,637 m và rộng 2,756 m vượt mốc xe buýt thường (dài 10,0–12,5 m; rộng 2,50–2,60 m); cần đối chiếu lại chiều dài thân và phần nhô ngoài trước khi chấp nhận.

Chưa có biên bản vòng hiệu chuẩn với lớp. Vùng phía sau xe buýt và cụm dưới mặt đường không được suy diễn thành vật mới. Các điểm chưa chắc được nêu ở mục 4 và 6 thay vì ghi “không có vật bỏ sót”.

## 4. Bốn hiện tượng chất lượng

Tất cả ảnh lấy từ task cá nhân D13-2A202603232, không mượn nhãn người khác. Chú thích trong ảnh là phần giải thích góc nhìn; không thay đổi dữ liệu hay cuboid trên CVAT.

| Hiện tượng / ảnh | Quan sát | Nguyên nhân giả thuyết → hệ quả khi gán |
|---|---|---|
| Bóng che — `t4-1-bong-che.png`, s01f18 | Góc nhìn từ trên cho thấy vùng xa LiDAR hơn xe buýt ít điểm hơn vùng đường bên cạnh | Xe buýt chắn tia quét; vùng thiếu điểm là chưa quan sát được. Chỉ suy phần bị che khi đủ cơ sở và dùng occluded cho vật thực sự bị che |
| Thưa ở xa — `t4-2-thua-o-xa.png`, s01f18 #6 | Car ở 47,01 m còn 49 điểm; Top/Side/Front chỉ hiện các điểm rải rác | Khoảng cách làm giảm mật độ, có thể kèm che khuất. Dùng camera xác nhận, không kéo hộp theo từng điểm đơn lẻ |
| Ít điểm bất thường — `t4-3-phan-xa-thap.png`, s04f15 #4 | Hộp Car ở 30,92 m có 0 điểm, ba mặt chiếu trống; nhãn đang có occluded=1 | Phản xạ thấp là một khả năng, nhưng che khuất hoặc hộp sai vị trí cũng giải thích được. Không kết luận vật tối màu hay phản xạ thấp từ hộp rỗng; cần kiểm camera sau trước khi chấp nhận |
| Điểm lạ — `t4-4-diem-la.png`, s01f18 | Góc nhìn nghiêng thấy điểm rời dưới lớp mặt đường; dữ liệu có điểm tại (4,92; −47,29; −22,14) m | Có thể do phản xạ đa đường hoặc sai đo; không coi là vật thực dưới đường và không vẽ cuboid cho cụm này |

Trong s01f18 có 19 điểm với z < −5 m trong bán kính ngang 50 m. Đây là phép đếm trực tiếp từ point cloud, không phải số vật. Nguyên nhân vật lý của các điểm sâu dưới đường chưa được xác minh.

## 5. Số liệu và tự kiểm

`dist_m = √(x²+y²)`, `r = √(x²+y²+z²)`, `z đáy = z − h/2`; số được tính từ bảng A đã làm tròn.

| Cuboid | z (m) | h (m) | z đáy (m) | dist_m (m) | r (m) | yaw (°) |
|---|---:|---:|---:|---:|---:|---:|
| s01f18 #2 Car | -1.051 | 1.473 | -1.788 | 6.59 | 6.670 | 268.5 |
| s01f18 #4 Bus | -0.397 | 3.158 | -1.976 | 27.84 | 27.839 | 272.0 |
| s01f52 #11 Pedestrian | -0.326 | 1.720 | -1.186 | 13.68 | 13.686 | 0.0 |

Gần nhất: s03f06 #5 Car, dist_m = 3.13 m, r = 3.128 m.

Xa nhất: s04f49 #10 Bus, dist_m = 108.81 m, r = 108.813 m.

Ground gap trong phiếu dùng mặt đường cục bộ; đáy gần −1,7 m chỉ phù hợp mốc tham chiếu. Car s01f18 #2 phía trước có yaw 268,5° gần 270°; Car s01f18 #6 ở phía sau có yaw 85,0° gần 90°, hướng ngược với trục tiến của xe thu dữ liệu. Một ảnh tĩnh không đủ khẳng định vận tốc hay chiều chuyển động thực tế.

| Car | dist_m (m) | points_in |
|---|---:|---:|
| s03f46 #4 Car | 4.58 | 2556 |
| s03f06 #4 Car | 15.00 | 715 |
| s02f07 #3 Car | 25.45 | 200 |
| s05f45 #2 Car | 40.94 | 119 |

Từ mẫu 4,58 m đến 40,94 m, khoảng cách tăng 8,94 lần và số điểm giảm từ 2556 xuống 119 (còn 4,7%). Xu hướng giảm phù hợp vật xa thưa điểm, nhưng số điểm trong hộp chưa chuẩn hóa theo kích thước và che khuất. Mốc tham khảo bài học cho Car là 1968/718/214/72 điểm ở khoảng 0–10/10–20/20–30/30–40 m; không dùng để tự động loại nhãn.

Các cảnh báo cần rà soát trong bản mới:

| Cuboid | Cảnh báo |
|---|---|
| s01f18 #7 Car | length=1.59 ngoài khoảng 3.0–6.0 của Car; width=0.90 ngoài khoảng 1.4–2.3 của Car; height=1.04 ngoài khoảng 1.2–2.3 của Car |
| s01f18 #8 Car | length=1.38 ngoài khoảng 3.0–6.0 của Car; width=4.05 ngoài khoảng 1.4–2.3 của Car; length < width: mũi tên yaw có dọc thân xe không? |
| s01f18 #9 Car | length=1.03 ngoài khoảng 3.0–6.0 của Car; width=4.70 ngoài khoảng 1.4–2.3 của Car; length < width: mũi tên yaw có dọc thân xe không? |
| s01f52 #1 Car | chồng lên hộp 13 (mỗi vật 1 hộp) |
| s01f52 #2 Car | 71 điểm sát ngay ngoài mép (quá chật, hay vật bên cạnh?) |
| s01f52 #6 Car | đáy thấp hơn mặt đường 0.27 m (chìm?); chồng lên hộp 14 (mỗi vật 1 hộp) |

Bản trước có 198 hộp và 117 hộp cảnh báo; bản mới có 126 hộp và 40 hộp cảnh báo. Đối chiếu hai bản CSV ở cùng vị trí x/y ghi nhận hai thay đổi kích thước: s04f15 #17 cũ → #8 mới từ 4,490 × 1,903 × 1,612 m thành 4,908 × 2,054 × 1,954 m; s05f14 #4 cũ → #3 mới giảm cao từ 1,475 m xuống 1,267 m. Đây là thay đổi đã lưu giữa hai bản xuất, không khẳng định thời điểm hoặc lý do thao tác nếu chưa có nhật ký.

Các cảnh báo vẫn được giữ nguyên trong phiếu để truy vết, không sửa số CSV nhằm tắt cờ. Có 12 hộp length < width cần kiểm hướng trục; 4 hộp vượt giới hạn 50 m: s01f18 #7 (62,08 m), s01f52 #18 (50,75 m), s04f15 #15 (52,03 m), s04f49 #10 (108,81 m). Các trường hợp này chưa được xác nhận đạt guideline.

Đã đối chiếu trực tiếp Car mẫu: 4,742 m chiều dài và 6,59 m khoảng cách khớp bảng A; phiếu có 2146 điểm. Đây là kiểm nhất quán dữ liệu, không thay thế kiểm chéo với bạn học. Chưa có MSSV hoặc kết quả kiểm chéo để ghi nhận.

## 6. Ý nghĩa nhãn và giới hạn

Nhãn 3D dùng mét vì biểu diễn vị trí và kích thước trong không gian cảm biến. Vật thưa điểm cần quy tắc về độ chắc chắn để tránh biến suy đoán thiếu bằng chứng thành nhãn huấn luyện.

Nguồn: `submission/D13-2A202603232-bangA.csv`, `submission/D13-2A202603232-tukiem.csv` và bản JSON API lưu local tại `work/cvat-export/D13-2A202603232-task38.json`.

Bộ ảnh gồm đủ 11 ảnh bắt buộc (4 ảnh đọc frame, 3 cuboid mẫu, 4 hiện tượng) và 8 ảnh tổng quan bổ sung, đóng gói trong `work/K5-L23-DAY13-2A202603232-images.zip`. Bản dữ liệu nhãn và hai bảng được nén riêng thành `work/D13-2A202603232-labeled-data.zip`. Ảnh và ZIP giữ local, không đưa lên repo công khai.

Báo cáo và bộ ảnh đã đủ các mục minh chứng theo cấu trúc nộp bài. Các giới hạn chất lượng còn lại gồm hộp rỗng, hướng dài/rộng, nhãn vượt 50 m, khả năng bỏ sót xe hai bánh và kiểm chéo chưa thực hiện. Không đồng nhất việc đủ file với việc tất cả nhãn đã đạt chuẩn. ZIP được giữ tại máy để nộp LMS; chưa thực hiện gửi bài lên LMS.
