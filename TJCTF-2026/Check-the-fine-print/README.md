Binwalk ta thấy có rất nhiều ảnh được ẩn bên trong, có tổng cộng 248 bức ảnh và nội dung của mỗi bức ảnh tương ứng với số trên tên của bức ảnh đấy.
<img width="1107" height="475" alt="image" src="https://github.com/user-attachments/assets/ff033b97-ed03-43c6-a8e1-af3305e0bb78" />
<img width="2967" height="1430" alt="image" src="https://github.com/user-attachments/assets/ff6e4392-a59e-40d3-8d1d-edc5a7a49be8" />


Sau đó ta thấy IDAT trước của ảnh chỉnh sửa logo.png không có gì mà các ảnh được săn bên trong lại có các ký tự trước IDAT như : .IDATx, #IDATx, BIDATx,..

<img width="1945" height="297" alt="image" src="https://github.com/user-attachments/assets/495b7da4-93b5-403b-8186-c68495f57e7b" />
<img width="1832" height="650" alt="image" src="https://github.com/user-attachments/assets/4a334717-fef5-4921-860a-630b1fc7e810" />

Ta vào từng ảnh nhỏ để xem.

Soi kĩ thì thấy nó lệch nhau đúng 1 byte tại offset 0x10A.

<img width="1642" height="477" alt="image" src="https://github.com/user-attachments/assets/41439c31-5e97-47ef-bd0e-22fda13f24c8" />
<img width="1777" height="505" alt="image" src="https://github.com/user-attachments/assets/5d5a8095-50f5-4a67-a87a-bfb5119023df" />
Cấu trúc của chunk IHDR trong ảnh .png
```text
Width (4 byte)
Height (4 byte)
Bit depth (1 byte)
Color type (1 byte)
Compression method (1 byte)
Filter method (1 byte)
Interlace method (1 byte)
```
IHDR ảnh 1:
```text
00 00 00 13 00 00 00 09 08 02 00 00 00
```
IHDR ảnh 2:
```text
00 00 00 13 00 00 00 09 08 02 01 00 00
```
Các byte này chính là byte 0 và 1 nằm sau 08 02 --> Đó là trường Compression method

### CRC là gì?
Đã đăng tải dưới dạng CRC 4 byte [loại khối + dữ liệu khối]
(tên của khối + phần chứa cấu trúc thông tin)
Tác giả đã thay đổi trường thuộc tính làm phần mềm tính lại mã CRC cho khối IHDR, kết quả nó nhảy sang một kí tự hoàn toàn khác mà strings đọc được

### Script Python gom 248 bit tự động:

```python
import os
bits = []
for i in range(1, 249):
    name = f"{i:03d}.png" if os.path.exists(f"{i:03d}.png") else f"{i}.png"
    if os.path.exists(name):
        with open(name, "rb") as f:
            f.seek(26)
            bits.append(str(f.read(1)[0]))
    else:
        bits.append("?")
print("".join(bits))
