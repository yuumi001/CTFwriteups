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
# 3. PyRev
[Source code]()  
Mới đầu nhìn vào mình có hơi rối khi mà nhìn vào. Vậy nên mình đã liệt kê các hàm ra và bắt đầu phân tích:
```
	def ispclub(cre):
	def prompt():
	def obfuscate(bys):
	def crypt(sor):
	def grant():
	def punish():
	def main():
main()
```
Ta có thể thấy `main()` được gọi cuối cùng để thực thi. Hãy cùng phân tích `main()`  
```
def main():
  sik1 = prompt()
  sik = obfuscate(sik1)
  sik = crypt(sik)
  sik = ispclub(sik)
  if (sik=="61ch4ll691ch4l..."):
    grant()
  else:
    punish()
```
Khi xem xét thì ta có thể thấy:
- `sik1 = prompt()`: Gọi đến 1 hàm input
- `obfuscate() | crypt() | ispclub()`: các hàm để encode string được input
- `if (sik=="...")`: So sánh string sau khi encode với 1 string khác, đúng -> `grant()`, sai -> `punish()`
Okay, giờ đến phân tích sâu vào nào.  
`grant()` và `punish()` là 2 hàm xuất thông báo khi kiểm tra điều kiện và cái ta muốn là hàm `grant()` được thực thi.  
  
Hai hàm trên khá đơn giản nên mình sẽ không phân tích nhiều và tập trung vào các hàm dùng để encode. Và mình đã tách từng hàm và chạy riêng để phân tích vì các hàm này không liên quan đến nhau.  
  
Đầu tiên là `ispclub()`  
```
C:\ISP\source-code\TrainingGII>python
Python 3.7.7
Type "help", "copyright", "credits" or "license" for more information.
>>> strTest="ABCD"				# test string
>>>
>>> def ispclub(cre):				# input strTest="ABCD"
...     sto=[]					# sto = []
...     gre=""      
...     for i in cre:				# với vòng lặp đầu
...             sto.append(i+str(len(i)))	# sto = ['A1']
...             sto.append("ch4ll"+i)		# sto = ['A1', 'ch4llA']
...     for i in sto:				# sto = ['A1', 'ch4llA', 'B1', 'ch4llB', 'C1', 'ch4llC', 'D1', 'ch4llD']
...             gre+=i
...     return gre
...
>>> ispclub(strTest)
'A1ch4llAB1ch4llBC1ch4llCD1ch4llD'		<-- output string
>>>
>>>
>>> # Ta có thể dễ dàng thấy được các ký tự mà ta cần sẽ các nhau 1 khoảng bằng 8. Từ đó mình có đoạn code sau:
>>> def solve_ispclub(indata):
...     return indata[::8]			# string[start:end:step]
...
>>> solve_ispclub('A1ch4llAB1ch4llBC1ch4llCD1ch4llD') 	<-- input
'ABCD'							<-- output
>>>
```  

Tiếp đến là hàm `crypt()`
  
```
>>> def crypt(sor):				# input "ABCD"
...     sro=[]					# sro = []
...     fusc="696"
...     for i in range(len(sor)):
...             sro.append(sor[i]+str(i))	# sro = ['A0', 'B1', 'C2', 'D3']
...     sro.reverse()				# sro = ['D3', 'C2', 'B1', 'A0']
...     for i in sro:
...             fusc+=i
...     return fusc				# fusc = "696" + "D3C2B1A0"
...
>>> crypt(strTest)
'696D3C2B1A0' 	<-- output string
>>>
>>>
>>> # Đầu tiên cần loại bỏ `696` ở đầu string, tiếp đó là đảo lại và lấy các ký tự thứ 2. Từ đó sẽ có:
>>> def solve_crypt(indata):	# indata = "696D3C2B1A0"
... 	out=""
... 	indata = indata[3:]	# indata = "D3C2B1A0"
... 	indata = indata[::-1]	# indata = "0A1B2C3D"
... 	cnt=1			# tạo biến đếm
... 	while (len(indata)): 	# Lặp lần đầu
... 		out+=indata[1]	# lấy ký tự thứ 2(indata[1]="A")
... 		indat=indata[len(str(cnt))+1:] 	<-- indata = "1B2C3D"
... 		cnt+=1
... 	return out
...
>>> solve_crypt('696D3C2B1A0') 	<-- input
'ABCD'		<-- output
```
Lý do cần thêm biến `cnt` là vì `crypt()` chạy vòng lặp từ `0` đến `len(sor)` nếu lặp lớn hơn 10 thì `len(sor)` sẽ lớn hơn 1.  
  
Tiếp đến là `obfuscate()`:  
```
>>> import base64 as isp 				# import base64 và rename thành isp
>>> def obfuscate(bys):
...     fusc = isp.b64encode(bys)			# fusc = b'QUJDRA=='
...     fusc += b"ispclub6910832"			# fusc = b'QUJDRA==ispclub6910832'
...     fusc = str(fusc)				# <class 'bytes'> đổi thành <class 'str'>
...     fusc = fusc[2:len(fusc)-1]			# loại bỏ prefix, fusc = "QUJDRA==ispclub6910832"
...     refus = []
...     for i in fusc:
...             refus.append(str(i))			
...             fusc="imustDOTHISCHALL011014"	# lưu giá trị vào refus, fusc="imustDOTHISCHALL011014"
...     for i in refus:
...             fusc+=i 				# fusc = "imustDOTHISCHALL011014" + "QUJDRA==ispclub6910832"
...     return fusc
...
>>> obfuscate(b'ABCD')
'imustDOTHISCHALL011014QUJDRA==ispclub6910832'
>>>
>>>
>>> # Vậy hàm `obfuscate()` chỉ đơn giản là encode base64 và thêm 2 chuỗi vào đầu và cuối. Từ đó hàm solve sẽ là:
>>> def solve_obfuscate(indata):
...     indata = indata.replace("imustDOTHISCHALL011014",'') 	# indata = "QUJDRA==ispclub6910832"
...     indata = indata.replace("ispclub6910832",'')		# indata = "QUJDRA=="
...     indata = indata.encode('utf-8')			        # indata = b'QUJDRA=='
...     return str(base64.b64decode(indata))			# decode indata và return giá trị 
...
>>> solve_obfuscate('imustDOTHISCHALL011014QUJDRA==ispclub6910832')
"b'ABCD'"
>>>
```
Bước cuối cùng là ghép các hàm đã tạo ra để tạo file [solve](htttps://pastebin.com/)
*các bạn cũng có thể tham khảo file [solve](https://pastebin.com/) này*
```
C:\ISP\source-code\TrainingGII> solve.py
b'ispclub{5up3r_345Y_cH4ll3ng3}'

C:\ISP\source-code\TrainingGII>
```
Flag: `ispclub{5up3r_345Y_cH4ll3ng3}`
