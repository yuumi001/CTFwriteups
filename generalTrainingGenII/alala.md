# 2. Super panic
[Super-panic](https://github.com/ispclub/generalTraining/blob/main/ctf/panic.jpg)  
Trước tiên mình sẽ check file bằng tool [stegsolve](http://www.caesum.com/handbook/Stegsolve.jar) và mình đã tìm thấy 1 QR code:
![foundQR](https://drive.google.com/uc?export=view&id=1KY5i--RmZZlEoJoHzS0GDYVuiFrFrOFK)  
Sau khi quét QR mình tìm được `ispclub{h3y_br0_` điều này có nghĩa là vẫn còn 1 nửa flag nữa còn thiếu. Mình đã quyết định check ảnh bằng HxD.  
Phần mở đầu của file khá bình thường khi mà mình không thấy có điểm gì kì lạ:
```
89 50 4E 47 0D 0A 1A 0A | 00 00 00 0D 49 48 44 52
         ^                         ^
   PNG signature               IHDR chunk
```
Mọi việc kiểm tra các chunk đều bình thường cho đến khi mình check IEND chunk:
```
49 45 4E 44 AE 42 60 82 | 00 00 00 9D FF F3 08 2A 82 A0 08 2A
         ^                                 ^
     IEND chunk                  Đoạn hex phía sau IEND chunk
```  
Một bức ảnh png sẽ kết thúc ở IEND và những thứ sau đó sẽ không được hiện thị nhưng ở bức ảnh này vẫn còn thêm 1 đoạn hex khá dài ở phía sau nữa nên mình liền nghĩ tới là chèn ảnh vào ảnh và mình đã check thử xem có tìm thấy file signature không. Và kết quả là không tìm ra gì cả. Cho đến khi mình check từ phía dưới lên:  
`10 00 64 94 64 A4 01 00 0E FF 8D FF` mình nhận thấy có điểm gì đó lạ ở đây và mình đã đúng `FF D8 FF E0 00 10 4A 46 49 46 00 01 <-- JPG signature` vậy là đây là mã hex của 1 bức ảnh bị đảo sau đó chèn vào.  
Vậy thì đảo nó lại để lấy ảnh thôi! 
Ở đây mình đã copy toàn bộ mã hex của cả 2 bức ảnh để đảo, ở đây mình tạm gọi phần trên là pic1 phần dưới là pic2 và nó bị đảo nên sẽ là pic2rev-ed.  
Vậy ta sẽ có như sau:
```
    - Ảnh gốc         sau khi đảo toàn bộ hex       Ảnh sau đảo
    pic1-pic2rev                -->                pic2-pic1rev
```
Vì toàn bộ sau khi đảo thì dữ liệu của pic2 sẽ được hiển thị và pic1 sẽ bị ngắt bởi end của pic2 giống như ban đầu khi ta chỉ thấy pic1 vậy.  
*Ảnh jpg có kết thúc là FF D9 vậy nên không cần lo chuyện sẽ có lỗi khi reverse toàn bộ bức ảnh*  
Mình đã copy toàn bộ mã hex của ảnh và dùng tool [Online hex tools](https://onlinehextools.com/reverse-hex-digits) để reverse toàn bộ mã hex sau đó copy trở lại HxD và save thành file có đuôi JPG:  
![flag2](https://drive.google.com/uc?export=view&id=1YjDus1-M_FF-nWt1gARIjIhlZpGbUe4k)  
Vậy là đã tìm thấy phần còn lại của flag: `c4lm_d0wn}`  
Flag: `ispclub{h3y_br0_c4lm_d0wn}`
