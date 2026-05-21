Đề bài ghi rõ “What if I make it so you need 3 keys to unlock the flag” nghĩa là mình cần 3 thứ để giải được bài này

Đầu tiên ta thử kiểm tra xem file chall.bin định dạng gì

<img width="1910" height="92" alt="image" src="https://github.com/user-attachments/assets/5355d7e1-0560-4bea-8a2a-b8a82d581c95" />

Sau đó ta check có chuỗi gì đặc biệt bằng strings

<img width="2625" height="272" alt="image" src="https://github.com/user-attachments/assets/81bfd205-5349-449a-a7ae-0c1f8a18d159" />
 Có khá ít chuỗi
 
 Soi hex bằng hxd:

<img width="1592" height="597" alt="image" src="https://github.com/user-attachments/assets/66afd3ed-8720-43ae-85e0-f78ce58da4fc" />

Ngoài các chuỗi đọc được thi sau dấu ':' toàn bộ dữ liệu là các byte ngẫu nhiên đặc trưng của dữ liệu đã bị mã hóa.

Ta sẽ thử thuật toán mã hóa đơn giản quen thuộc nhất là phép XOR

Thứ quan trọng mà ta biết là dạng chuẩn của cờ là tjctf{...} nên ta sẽ dò ngược được phần đầu của key 

Đổi định 6 kí tự cờ tjctf{ sang mã hex ta được
```
74 6A 63 74 66 7B
```
XOR 6 byte này với 6 byte đầu sau dấu ':'
```
1D 09 0D 07 67 0F
```

```
0x1D ^ 0x74 = 0x69 ( 'i' )
0x09 ^ 0x6A = 0x63 ( 'c' )
0x0D ^ 0x63 = 0x6E ( 'n' )
0x07 ^ 0x74 = 0x73 ( 's' )
0x67 ^ 0x66 = 0x01 
0x0F ^ 0x7B = 0x74 ( 't' )
```
Dãy kí tự sinh ra 'icnst...' hoàn toàn trùng khớp với chuỗi kí tự mà ta dùng strings liệt kê được

Từ đây ta sẽ kết hợp các cụm vào để xem key nào mới cho ra flag chuẩn

Script Python:
```
# 36 byte Ciphertext thực sự ở cuối file bin
ct = bytes.fromhex("1d090d07670f4404711b0c1e493202391c100640732b45056c0b26180e0b3e27130e5d0e")

# Danh sách các candidate do bạn đúc kết từ việc phân tích cấu trúc hex
candidates = [
    b"icns\x01ttf\x02xylzma",
    b"icns\x01ttf\x02xylzmaK",
    b"icns\x01ttf\x02xylzmaKLZMA_DATA:"
]

print("[*] Thử nghiệm giải mã dựa trên các mảnh ghép cấu trúc bạn chọn:")

for idx, key in enumerate(candidates):
    flag = bytearray()
    for i in range(len(ct)):
        flag.append(ct[i] ^ key[i % len(key)])
    
    print(f"\n-> Kết quả với Candidate {idx+1} (Độ dài {len(key)} bytes):")
    print(f"   Key: {key}")
    print(f"   Flag: {flag.decode('utf-8', errors='ignore')}")
```
Kết quả :
<img width="1915" height="640" alt="image" src="https://github.com/user-attachments/assets/80c79899-b0cd-4549-81a6-ff08dc92fd3e" />
Suy ra key gốc dạng text :
```
icnsttxylzmaK
```

Flag: tjctf{0bscur3_crush3r_1cns_ttf_lzm3}
