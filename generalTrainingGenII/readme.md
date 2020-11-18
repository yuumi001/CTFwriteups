# TrainingCTF2020
Writeup dành cho các challenges trong thời gian Training của Gen II CLB ISP.
  
  
  
## Overview
 | Title | Category  | Flag |
 | ------ | ------ | ------ |
 | [Sudoku](#1-sudoku) | Programming | `ispclub{5ud0ku_puzzl3_15_n0t_345y}` |
 | [Super panic](#2-super-panic) | Forensic | `ispclub{h3y_br0_c4lm_d0wn}` |
  
  
  
# 1. Sudoku  
[Source code](https://github.com/ispclub/generalTraining/blob/main/ctf/sudoku.cpp)

Đầu tiên mình chạy thử code và nhận được output sau:
```
	C:\ISP\source-code\TrainingGII>Sudoku.exe
	ispclub{d0_y0u_l1k3_5ud0ku?}
	(Y or N)? ====> Answer: N
	
	ispclub{y0u_d0_n0t_g3t_th3_fl4g}
  
	C:\ISP\source-code\TrainingGII>Sudoku.exe
	ispclub{d0_y0u_l1k3_5ud0ku?} 
	(Y or N)? ====> Answer: Y
	
	ispclub{l3t'5_try_4_puzzl3!}
							
	--------------------------
	| 7 0 0 | 6 0 0 | 2 0 3 |
	| 0 2 0 | 8 1 0 | 0 5 0 |
	| 0 0 5 | 0 9 2 | 0 0 0 |
	--------------------------
	| 2 0 0 | 0 7 0 | 5 0 8 |
	| 3 0 7 | 0 0 9 | 0 2 0 |
	| 0 9 0 | 0 4 0 | 0 0 7 |
	--------------------------
	| 1 0 8 | 0 3 7 | 0 0 2 |
	| 0 0 0 | 1 0 0 | 8 0 0 |
	| 6 0 2 | 0 0 0 | 9 3 0 |
	--------------------------
	Try again
```
Sau khi chạy cả 2 trường hợp "Y" và "N" mình nhận được tất cả 3 flag giả:  
```
  ispclub{d0_y0u_l1k3_5ud0ku?} 
  ispclub{l3t'5_try_4_puzzl3!}
  ispclub{y0u_d0_n0t_g3t_th3_fl4g}
```
Cùng check qua source code xem ta sẽ thu được gì:
- Dòng 20: *string sudoku="..."*  có 81 ký tự = 9x9. Có thể suy ra đây là ma trận sudoku.
- Dòng 37: *sudoku[++c]-97* dùng để chuyển các ký tự từ chữ về số theo mã ASCII.
- Dòng 42 - 45: Ta thấy string flag4 phụ thuộc vào các string flag1, flag2, flag3 và "sudoku". 
- Dòng 47: *if (flag4.size()==34) cout << flag4 << endl;*  nếu size flag4 bằng đúng với 34 ( = với size của flag4 dòng 19) thì sẽ in ra flag4. Vậy ta kết luận flag4 là flag của bài.  

Bây giờ ta phải đi giải bài sudoku này, thay các giá trị 0 bằng những số thích hợp. Ta chuyển dãy sudoku ấy về xâu chữ cái với *‘0’ = ’a’, ’1’ = ’b’, ’2’ = ’c’,...* Mình dùng 1 script `python` để làm việc này:  
```
C:\ISP\source-code\TrainingGII>python
Python 3.7.7
Type "help", "copyright", "credits" or "license" for more information.
>>> sudoku="haagaacadacaibaafaaafajcaaacaaahafaidahaajacaajaaeaaahbaiadhaacaaabaaiaagacaaajda"    <-- input string
>>> for i in sudoku:
...     print(ord(i)-97,end='')
...
700600203020810050005092000200070508307009020090040007108037002000100800602000930    <-- output string
>>>
``` 
Sau đó các bạn có thể tìm tool hoặc giải tay để luyện thêm trí não, còn ở đây mình tìm thấy 1 tool giúp giải sudoku khá tiện và mình đã chỉnh sửa lại để phù hợp giải chall này:  
[source code](https://pastebin.com/vmYLSBNP)  
```
C:\ISP\source-code\TrainingGII\>python Sudoku-solver.py
hbjgfecidecgibdhfjidfhjcebgcgedhbfjidihfgjbcefjbceidghbfijdhgecjedbcgihfghceifjdb   <-- output string
C:\ISP\source-code\TrainingGII\>
```
Sau đó thay chuỗi vừa tìm được vào source code challenge ban đầu và chạy lại:  
```
	C:\ISP\source-code\TrainingGII>Sudoku.exe
	ispclub{d0_y0u_l1k3_5ud0ku?}
	(Y or N)? ====> Answer: Y
	ispclub{l3t'5_try_4_puzzl3!}
	--------------------------
	| 7 1 9 | 6 5 4 | 2 8 3 |
	| 4 2 6 | 8 1 3 | 7 5 9 |
	| 8 3 5 | 7 9 2 | 4 1 6 |
	--------------------------
	| 2 6 4 | 3 7 1 | 5 9 8 |
	| 3 8 7 | 5 6 9 | 1 2 4 |
	| 5 9 1 | 2 4 8 | 3 6 7 |
	--------------------------
	| 1 5 8 | 9 3 7 | 6 4 2 |
	| 9 4 3 | 1 2 6 | 8 7 5 |
	| 6 7 2 | 4 8 5 | 9 3 1 |
	--------------------------
	ispclub{5ud0ku_puzzl3_15_n0t_345y}
```
Flag: `ispclub{5ud0ku_puzzl3_15_n0t_345y}`  

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
*Ảnh jpg có kết thúc là FF D9 vậy nên không cần lo chuyện sẽ có lỗi khi reverse toàn bộ bức ảnh*  
Mình đã copy toàn bộ mã hex của ảnh và dùng tool [Online hex tools](https://onlinehextools.com/reverse-hex-digits) để reverse toàn bộ mã hex sau đó copy ngược trở lại HxD và save thành file có đuôi JPG:  
![flag2](https://drive.google.com/uc?export=view&id=1YjDus1-M_FF-nWt1gARIjIhlZpGbUe4k)  
Vậy là đã tìm thấy phần còn lại của flag: `c4lm_d0wn}`  
Flag: `ispclub{h3y_br0_c4lm_d0wn}`
