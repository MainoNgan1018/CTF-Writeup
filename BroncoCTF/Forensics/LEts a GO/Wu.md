### Đề bài
<img width="1440" height="677" alt="image" src="https://github.com/user-attachments/assets/8346d727-c47a-4f4c-8f0a-8423e0fc8eaa" />
<img width="1645" height="1315" alt="image" src="https://github.com/user-attachments/assets/7d5e0603-1002-4ddb-bc47-20522e610782" />
Có rất nhiều tệp .part_xx và thư mục cùng gợi ý Go step by step, brick by brick" là manh mối của dạng bài File Carving hoặc Reconstruction (lắp ghép lại dữ liệu bị phân mảnh).

Ta thử chạy lệnh để tìm kiếm tất cả các chuỗi có dấu { như flag
```
grep -r "{" .
```
> -r: Yêu cầu grep đi vào tất cả các thư mục con bên trong thư mục hiện tại.

> .: Chỉ định vị trí bắt đầu tìm kiếm là thư mục bạn đang đứng.

<img width="2305" height="140" alt="image" src="https://github.com/user-attachments/assets/387b0bbd-1632-4235-bccf-8d1aade7de83" />

Ta đã tìm thấy tệp `src/modules/network/.solver.py` , đây khả năng là nơi tác giả giấu logic lắp ghép các tệp .part_*

Xem nội dung file này:

<img width="2780" height="1150" alt="image" src="https://github.com/user-attachments/assets/9d88a6d6-e5db-4572-9afa-dfcdbae211f8" />

Phân tích:

* Nó sử dụng os.walk(start_dir) để quét toàn bộ thư mục và các thư mục con.
* Nó tìm các tệp có định dạng tên khớp với regex ^\.part_(\d+)$ (ví dụ: .part_00, .part_01,...) và chỉ lấy các file có chỉ số từ 0 đến 49.
* Nó đọc nội dung của từng tệp, đưa vào một từ điển (dictionary) và sắp xếp chúng theo thứ tự chỉ số, sau đó nối (concatenate) lại thành một chuỗi duy nhất.

Thực thi script:
```
python3 src/modules/network/protocols/.solver.py .
```
Dấu . ở cuối lệnh đại diện cho thư mục hiện tại, giúp script quét toàn bộ các file .part_ nằm trong các thư mục con

<img width="2787" height="362" alt="image" src="https://github.com/user-attachments/assets/6272f7dc-0f67-4609-bc1e-68f3cd1cbf07" />

Nó bị lỗi ở 2 dòng có thể do môi trường nên ta chạy lệnh `sed` để xóa bỏ dòng 2 chứa lỗi đi:
```
sed -i '2d' src/modules/network/protocols/.solver.py
```
Sau đó chạy lại script:

<img width="2787" height="220" alt="image" src="https://github.com/user-attachments/assets/b5983769-311b-41d0-8140-beb207790743" />
Flag: bronco{3ve4yth1ng_1s_aw3s0me}
