#Binary Digits

**Bản chất của dữ liệu số:**

Nếu ta đọc 8bit 1 lúc, ta có văn bản (ASCII)

Nếu ta đọc 24 bit 1 lúc, ta có màu sắc (RGB)

Nếu ta xếp chúng lên một mặt phẳng tọa độ, ta có hình ảnh

Các chuỗi 0 và 1 này có thể đại diện cho các ký tự ASCII

Chia chuỗi thành các nhóm 8 bit, mỗi nhóm này sẽ tương ứng với một giá trị thập phân và từ đó chiếu lên bảng mã ASCII

Sử dụng (https://gchq.github.io/CyberChef/) CyberChef 

Ta dùng module “From Binary” nó chuyển chuỗi 01 về các byte gốc, và các byte đó bắt đầu bằng JFIF (magic bytes của file ảnh jpg) -> file thực chất là ảnh jpg đã bị chuyển sang dạng nhị phân.



Thêm module “Render Image” có tác dụng biến các byte dữ liệu thô thành một hình ảnh được hiển thị trên màn hình -> flag




