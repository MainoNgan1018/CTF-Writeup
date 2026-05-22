# Đề bài :
<img width="512" height="176" alt="image" src="https://github.com/user-attachments/assets/f8ecb87f-47f5-4190-b448-a9932fe0c432" />

<img width="3040" height="1230" alt="image" src="https://github.com/user-attachments/assets/c8abc4b9-70a0-42fe-a99f-69873ac39c16" />


Trích xuất payload thô:
```
Nhấn chuột phải vào gói tin bất kỳ -> Chọn Follow -> UDP Stream.
Ở phần hiển thị bên dưới (Show data as), chuyển từ ASCII sang Raw.
Nhấn Save as.
```
Vào hxd xem hex của file audi.raw này:

<img width="1633" height="1336" alt="image" src="https://github.com/user-attachments/assets/c89ae684-523b-4c76-8271-9b0606110184" />

Khoảng cách từ cụm magic 12 34 56 78 của gói trước đến cụm magic 12 34 56 78 của gói sau được tính:
```
Mốc gói sau - Mốc gói trước = 0xB4 - 0x0B = 0xAC (172 bytes)
```
Mỗi gói tin thực chất chứa một Header dài 12 bytes + phần dữ liệu thực tế dài 160 bytes

Cấu trúc 12 bytes header lặp lại mà ta thấy trong file hex:
```
80 00 03 E8 00 00 00 00 12 34 56 78
```
Đây chính xác là cấu trúc của giao thức RTP (Real-time Transport Protocol) – giao thức chuyên dùng để truyền tải dữ liệu âm thanh/video trong các cuộc gọi VoIP!

Do người ra đề cấu hình chạy trên cổng UDP lạ (10000 -> 20000) nên ban đầu Wireshark chỉ nhận diện chung chung là gói UDP thô.
```
80: Phiên bản RTP (Version 2).
00: Payload Type (Mã hóa loại âm thanh). Giá trị 00 trong chuẩn RTP đại diện cho codec PCMU (G.711 $\mu$-law) 
03 E8: Sequence Number (Số thứ tự gói tin, ở đây gói đầu là 1000).
00 00 00 00: Timestamp.
12 34 56 78: SSRC Identifier (Định danh nguồn đồng bộ dòng dữ liệu).
```
Chuyển UDP -> RTP
```
chuột phải vào một gói tin bất kỳ trong danh sách -> Chọn Decode As...
Ở cột Current, hãy chuyển từ UDP sang RTP.
Nhấn Apply.
```
<img width="3020" height="1495" alt="image" src="https://github.com/user-attachments/assets/6a5cd167-2b78-4d4e-b85b-681497de249a" />

Soi payload và so sánh gói 2 - gói 7 :
<img width="2976" height="1713" alt="image" src="https://github.com/user-attachments/assets/6ddf6613-4a78-4401-b1fc-57011c8dfeb8" />
<img width="3045" height="1727" alt="image" src="https://github.com/user-attachments/assets/853b0cb1-8e82-4956-965e-b4fe2d979997" />
Ta thấy 6 bytes đầu của 2 gói:
Gói 2
```
00 00 00 0d 78 18
```
Gói 7
```
00 00 01 0d 79 18
```
Byte tại index 2:

Gói 2: 00 -> nhị phân : 00000000 (lsb 0)

Gói 7: 01 -> nhị phân : 00000001 (lsb 1)

Byte tại index 4:

Gói 2: 78 -> nhị phân : 01111000 (lsb 0)

Gói 7: 79 -> nhị phân : 01111001 (lsb 1)

Kết luận:
Các byte ở vị trí lẻ giống nhau 100% nhưng ở vị trí chẵn lại sai khác, và các gói 3-8, 4-9,.. cũng thế.

Nên ta suy ra chỉ cần lọc lsb index chẵn của 5 gói RDP đầu

Mỗi gói tin 160 bytes payload , lọc ra được chính xác 80 bytes ở vị trí chẵn <=> 80 bit lsb

Sau khi duyệt toàn bộ các gói tin, cắt chuỗi 8 bit thành 1 byte để chuyển sang hệ thập phân dịch sang kí tự ASCII

Script Python:
```bash
with open("audi.raw", "rb") as f:

    data = f.read()

 

packet_size = 172

header_size = 12

 

bit_string = ""

 

# Duyệt qua từng gói tin 172 bytes

for i in range(0, len(data), packet_size):

    packet = data[i : i + packet_size]

    if len(packet) == packet_size:

        # Lấy phần payload âm thanh (160 bytes)

        audio_payload = packet[header_size:]

        

        # Duyệt qua các byte ở vị trí CHẴN (index 0, 2, 4, 6, ...)

        for idx in range(0, len(audio_payload), 2):

            byte_val = audio_payload[idx]

            

            # Lấy bit cuối cùng (LSB) của byte đó

            lsb = byte_val & 1

            bit_string += str(lsb)

 

# Gom nhóm cứ 8 bits thành 1 byte hoàn chỉnh

extracted_bytes = bytearray()

for i in range(0, len(bit_string), 8):

    eight_bits = bit_string[i:i+8]

    if len(eight_bits) == 8:

        # Chuyển chuỗi bit thành số nguyên tương ứng

        extracted_bytes.append(int(eight_bits, 2))

 

# Lưu thành quả ra file mới

with open("lsb_secret.bin", "wb") as f:

    f.write(extracted_bytes)

 

print("Đã trích xuất LSB xong! Hãy kiểm tra file lsb_secret.bin")
```
Chạy trên wsl:
<img width="1280" height="51" alt="image" src="https://github.com/user-attachments/assets/e0822bb3-4198-47f6-9d32-9e55a2c02262" />

Kết quả :
<img width="2627" height="1117" alt="image" src="https://github.com/user-attachments/assets/3001a7d4-c73e-43a3-bead-c80cd53be57f" />

<img width="1280" height="63" alt="image" src="https://github.com/user-attachments/assets/e2c09851-1084-4422-a5cd-82ce487cdf51" />

Flag: tjctf{h3y_v0ip_s73g_is_4_7hing}

