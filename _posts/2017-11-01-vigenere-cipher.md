---
layout: post
title: "Mã hoá Vigenère"
tags: Vigenere-Cipher Encryption Decryption
---
Chào các bạn, hôm nay chúng ta sẽ đến với một chủ đề về mã hóa: *Vigenère Cipher*.

Phương pháp trong mã hóa Vigenère được phát biểu lần đầu bởi Giovan Battista Bellaso trong cuốn *La cifra del. Sig. Giovan Battista Bellaso* từ thế kỷ 16. Sau này Blaise de Vigenère vào thế kỷ 19 công bố một phương pháp tương tự, nhưng mạnh hơn, người ta đã lấy tên Vigenère đặt cho tên mã hóa này. Nhiều người cho rằng điều đó là không công bằng cho người đã phát minh ra nó trước.

Nhưng thôi, tạm bỏ qua những vấn đề lịch sử, ta sẽ đến luôn với nội dung mã hóa.

## 1. Vigenère Cipher
### Encrypt
Mã hóa Vigenère là sự kết hợp xem kẽ nhiều phép mã hóa Caesar với các bước dịch khác nhau.

Nhắc lại một chút về mã hóa Caesar, đây là phương pháp mã hóa mà ta chọn ra một giá trị khóa key K, và dịch các chữ cái theo vòng tròn K bước. Ví dụ thông thường nhất là K=13 thì:
```js
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
```js
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

### Code
```py
rom itertools import cycle

class VigenereCipher (object):
    def __init__(self, key, alphabet):
        self.key = key.decode('utf-8')
        self.alphabet = alphabet.decode('utf-8')

    def cipher(self, mode, str):
        return ''.join(self.alphabet[(self.alphabet.index(m) +
                  mode * self.alphabet.index(k)) % len(self.alphabet)]
                  if m in self.alphabet else m for m, k in zip(str.decode('utf-8'),
                  cycle(self.key))).encode('utf-8')

    def encode(self, str):
        return self.cipher(1, str)
    def decode(self, str):
        return self.cipher(-1, str)
```

## 2. Vigenère Cipher auto key
Đối với Vigenère Cipher thông thường như trên, thì ta sẽ lặp lại key để có độ dài bằng với độ dài chuỗi cần mã hoá, theo đó thì ta chỉ cần tra bảng mã là ra chuỗi encode cũng như decode. Tuy nhiên, để tăng độ khó cho chuỗi mã hoá, người ta sử dụng một phương pháp khác có tên gọi là Vigenère Cipher auto key, trong đó ta sẽ sử dụng key *một lần duy nhất*, ghép với chuỗi cần mã hoá để tạo ra key mới như sau:

```js
origin key: password
message: my secret code i want to secure
key:     pa ssword myse c retc od eiwant
```
Ta sẽ tiến hành encode và decode như sau:

**Encode**
- Bước 1: Tách hoặc thêm các dấu cách (space) trong origin key để cùng form với chuỗi gốc. Ví dụ như trên với "my secret" thì ta phải tách "password" thành "pa ssword"
- Bước 2: Sau đó ghép với chuỗi gốc và cắt cho độ dài bằng độ dài chuỗi gốc. Ta được chuỗi key là "pa ssword myse c retc od eiwant"
- Bước 3: So sánh với bảng mã và đưa ra chuỗi mã hoá "by kwqihf aghg z atph ws aachkx"

**Decode**: Việc decode khó khăn hơn trước khá là nhiều khi ta chỉ biết được mỗi key, khi này ta phải cắt ghép từng bước để dần dần ra được chuỗi gốc:
- Bước 1: Nhận thấy rằng các dấu cách (space) ở chuỗi gốc và chuỗi đã mã hoá là giống nhau, vì thế giống như phần encode, ta tách hoặc thêm các dấu cách (space) trong origin key để cùng form với chuỗi đã mã hoá, ta được "pa ssword"
- Bước 2: So sánh với bảng mã và đưa ra được phần đầu của chuỗi gốc là "my secret"
- Bước 3: Ghép tiếp phần chuỗi gốc tìm được với key, thêm dấu cách (space) đề phù hợp với form của chuỗi đã mã hoá, ta được "pa ssword myse c ret"
- Bước 4+: Lặp lại các bước 2-3 cho đến hết chuỗi đã mã hoá, ta được chuỗi gốc là "my secret code i want to secure"

### Code:
```py
class VigenereAutokeyCipher:
    def __init__(self, key, abc):
        self.key = key
        self.abc = abc
        self.alle = len(abc)

    def cipher(self, s, m):
        output, keyarr = '', list(self.key)
        for char in s:
            try:
                output += self.abc[(self.abc.index(char) + self.abc.index(keyarr.pop(0)) * (-1, 1)[m]) % self.alle]
                keyarr.append((output[-1], char)[m])
            except ValueError:
                output += char
        return output

    def encode(self, s):
        return self.cipher(s, 1)

    def decode(self, s):
        return self.cipher(s, 0)
```

## 3. Conclusion
Vigenère Cipher là một mã hoá rất đơn giản nhưng cũng rất đẹp đẽ!

## 4. References
- [Vigenère Cipher](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher)
- [Vigenère Cipher Kata](https://www.codewars.com/kata/vigenere-cipher-helper/python)
- [Vigenère Cipher Autokey Kata](https://www.codewars.com/kata/vigenere-autokey-cipher-helper/python)
