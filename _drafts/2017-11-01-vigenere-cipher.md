---
layout: post
title: "Vigenère Cipher"
tags: Vigenere-Cipher Encryption Decryption
---
Chào các bạn, hôm nay chúng ta sẽ đến với một chủ đề về mã hóa: *Vigenere Cipher*.

Phương pháp trong mã hóa Vigenère được phát biểu lần đầu bởi Giovan Battista Bellaso trong cuốn *La cifra del. Sig. Giovan Battista Bellaso* từ thế kỷ 16. Sau này Blaise de Vigenère vào thế kỷ 19 công bố một phương pháp tương tự, nhưng mạnh hơn, người ta đã lấy tên Vigenère đặt cho tên mã hóa này. Nhiều người cho rằng điều đó là không công bằng cho người đã phát minh ra nó trước.

Nhưng thôi, tạm bỏ qua những vấn đề lịch sử, ta sẽ đến luôn với nội dung mã hóa.

## Vigenère Cipher
### Encrypt
Mã hóa Vigenère là sự kết hợp xem kẽ nhiều phép mã hóa Caesar với các bước dịch khác nhau.

Nhắc lại một chút về mã hóa Caesar, đây là phương pháp mã hóa mà ta chọn ra một giá trị khóa key K, và dịch các chữ cái theo vòng tròn K bước. Ví dụ thông thường nhất là K=13 thì:
```
A -> N
B -> O
C -> P
...
```

Trong mã hóa Vigenère thì ra sẽ sử dụng một bảng để làm phép dịch, và một chuỗi khóa gọi là *key*, thay vì một số như trong mã hóa Caesar.
![]({{ site.url }}/assets/images/Vigenere.png)

Ví dụ ta có một chuỗi cần mã hóa như sau:

ATTACKATDAWN

Và key dùng để mã hóa là "LEMON". Trước hết ta sẽ nhân chuỗi LEMON này lên để nó có cùng độ dài với chuỗi cần mã hóa:

LEMONLEMONLE

Khi này ta sẽ sử dụng bảng mã hóa như sau: bắt đầu từ trái qua phải, lấy ký tự của key làm *dòng*, ký tự của chuỗi cần mã hóa là *cột* và dóng vào trong bảng mã ta được một ký tự, ký tự đó chính là ký tự đã được mã hóa.

Áp dụng với key LEMONLEMONLE và chuỗi ATTACKATDAWN bên trên:
```
[L, A] -> L
[E, T] -> X
[M, T] -> F
[O, A] -> O
[N, C] -> P
[L, K] -> V
[E, A] -> E
[M, T] -> F
[O, D] -> R
[N, A] -> N
[L, W] -> H
[E, N] -> R
```
ta được chuỗi LXFOPVEFRNHR. Đơn giản đúng ko ?

### Decrypt 
Để giải mã, ta sẽ lần ngược lại bảng mã thôi. Bắt đầu từ trái qua phải, với mỗi ký tự của key làm *dòng*, ta tìm *cột* mà khi dóng xuống ta có gía trị là ký tự trong chuỗi đã mã hóa. Ký tự trong cột đó chính là ký tự của chuỗi ban đầu.

### Note
Trên thực tế, bảng mã không bắt buộc là 26 ký tự alphabet mà có thể tùy ý tùy vào người mã hóa, ví dụ như sử dụng ký tự tiếng Việt, tiếng Nhật hay bất kể ký tự gì. Tuy nhiên phương pháp mã hóa thì không thay đổi.

## Vigenere Cipher auto key
