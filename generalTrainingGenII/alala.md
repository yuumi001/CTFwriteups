# 2. Super panic
[Super-panic](https://github.com/ispclub/generalTraining/blob/main/ctf/panic.jpg)  
Trước tiên mình sẽ check file bằng tool [stegsolve](http://www.caesum.com/handbook/Stegsolve.jar) và mình đã tìm thấy 1 QR code:
![foundQR](https://drive.google.com/uc?export=view&id=1KY5i--RmZZlEoJoHzS0GDYVuiFrFrOFK)  
Sau khi quét QR mình tìm được `ispclub{h3y_br0_` điều này có nghĩa là vẫn còn 1 nửa flag nữa còn thiếu. Mình đã quyết định check ảnh bằng HxD.  
Phần mở đầu của file khá bình thường khi mà mình không thấy có điểm gì kì lạ:
```
89 50 4E 47 0D 0A 1A 0A | 00 00 00 0D 49 48 44 52
         ^                         ^
   PNG signature                  IHDR
```
Mọi việc kiểm tra đều bình thường cho đến khi mình check IEND chunk:
`49 45 4E 44 AE 42 60 82   <-- IEND chunk`  
Một bức ảnh png sẽ kết thúc ở IEND chunk nhưng ở bức ảnh này vẫn còn thêm 1 đoạn hex khá dài ở phía sau nữa nên mình liền nghĩ tới là chèn ảnh vào ảnh và mình đã check thử xem có tìm thấy file signature không. Và kết quả là không tìm ra gì cả. Cho đến khi mình check từ phía dưới lên:  
`10 00 64 94 64 A4 01 00 0E FF 8D FF` mình nhận thấy có điểm gì đó lạ ở đây và mình đã đúng `FF D8 FF E0 00 10 4A 46 49 46 00 01 <-- JPG signature` vậy là đây là mã hex của 1 bức ảnh bị đảo sau đó chèn vào.  
Vậy thì đảo nó lại để lấy ảnh thôi!
*ảnh jpg có kết thúc là FF D9 vậy nên không cần lo chuyện sẽ có lỗi khi reverse toàn bộ bức ảnh*  
Mình đã copy toàn bộ mã hex của ảnh và dùng tool [Online hex tools](https://onlinehextools.com/reverse-hex-digits) để reverse toàn bộ mã hex sau đó copy ngược trở lại HxD và save thành file có đuôi JPG:  
![flag2](https://drive.google.com/uc?export=view&id=1YjDus1-M_FF-nWt1gARIjIhlZpGbUe4k)  
Vậy là đã tìm thấy phần còn lại của flag: `c4lm_d0wn}`  
Flag: `ispclub{h3y_br0_c4lm_d0wn}`
