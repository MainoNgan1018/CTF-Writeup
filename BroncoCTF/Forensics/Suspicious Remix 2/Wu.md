### Đề bài
<img width="1455" height="1457" alt="image" src="https://github.com/user-attachments/assets/b4946ded-0d8b-46e5-ac08-1c224ed59d37" />

Vì đề bài gợi ý mật khẩu nằm trong file ảnh này nên ta thực hiện dò thông tin ẩn trong ảnh.

Cho luôn lên tool Aperi'Solve thì ta thấy hint được giấu sau lớp màu của tấm ảnh:

<img width="3305" height="1505" alt="image" src="https://github.com/user-attachments/assets/afd2bba3-fc6a-44c5-91b9-79400e5bd81b" />

<img width="727" height="605" alt="image" src="https://github.com/user-attachments/assets/b8c06f4f-6378-46ed-a4cd-ec532c0f1591" />

Nó ghi Mật khẩu = năm phát hành của bài hát không phải nhạc phim (non-OST)

Bài hát trong file `sg_remix2.wav` là một bản remix. Ta cần xác định bản gốc.

Mình cũng không biết tìm bản gốc như nào nên mình tra thông tin ca sĩ trên ảnh.

<img width="1695" height="992" alt="image" src="https://github.com/user-attachments/assets/edfa5ebc-29ef-4e56-99d6-75ee37724ef1" />

MV hot của Rick Astley được phát hành năm 1987 nhưng mình thử làm mật khẩu không được, năm 1986 năm sáng tác xong cũng sai, còn năm 1988 là năm nhận giải thưởng đầu lại ra.

Đề nhặc tới `steg command` khả năng cao đây là Steghide công cụ kinh điển thường được dùng để nhúng dữ liệu và file.

Lệnh trích xuất: 
```
steghide extract -sf sg_remix2.wav
```

<img width="2195" height="155" alt="image" src="https://github.com/user-attachments/assets/3d3a830e-f1be-4ce3-ba01-c1c99dd20fc5" />

Flag: bronco{7h3y_g07_y0u_4g4in_didn'7_7h3y?}
