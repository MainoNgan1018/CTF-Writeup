Không mở được ảnh nên ta soi hxd.

<img width="1950" height="1305" alt="image" src="https://github.com/user-attachments/assets/690a1ba2-a869-482b-9be6-547b8f99a12e" />

Ta có thể thấy magic number của ảnh đang sai. 

Tiếp đến là offset 0x10:
```
00 00 01 F4 00 00 00 00 08 02 00 00 00 00 00 00
```
Trong đó:

Width: `00 00 01 F4`

Height: `00 00 00 00`

```
E956SQ4:/mnt/c/Users/ADMIN/Downloads$ pngcheck challenge.png
challenge.png  invalid IHDR image dimensions (500x0)
```

Lỗi pngcheck báo 500x0 là hoàn toàn chính xác với dữ liệu thô này.

Mỗi chunk trong PNG có định dạng [4 bytes length][4 bytes type][n bytes data][4 bytes CRC].

Data: Là 13 bytes 
```
Width (4) + Height (4) + Bit depth (1) + Color type (1) + Compression (1) + Filter (1) + Interlace (1)
```
Vì Width, Bit depth, Color type... thường đã có sẵn (hoặc có thể đoán được), chiều cao (Height) chính là biến duy nhất gây ra sai lệch CRC.

Sử dụng công cụ TweakPNG để ta có thể thay đổi Height và TweakPNG sẽ tự động phát hiên CRC sai để tính lại chuẩn.

Trước khi cho ảnh vào thì ta phải sửa đúng Header không thì mọi trình xem ảnh kể cả TweakPNG đều báo `Invalid format`

Trong HxD, sửa 8 byte đầu từ
```
DE AD BE EF 00 00 00 00
```
thành:
```
89 50 4E 47 0D 0A 1A 0A
```

Mở TweakPNG:

<img width="1817" height="627" alt="image" src="https://github.com/user-attachments/assets/4f944cc9-aa66-4647-ad53-46fc42640845" />

Click đúp vào dòng IHDR, một cửa sổ nhỏ hiện ra, thay số 0 bằng số bất kì ( tôi thay 500 ), sau đó nhấn OK -> File -> Save.

<img width="1145" height="420" alt="image" src="https://github.com/user-attachments/assets/f839f317-caf4-473d-9d41-9dc05c40234a" />

Flag: bronco{wh4t_ar3_mag1c_byt3s}

