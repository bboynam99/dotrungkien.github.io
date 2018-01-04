---
layout: post
title:  "Factorials and Trailing zeros"
mathjax: true
tags: algorithm trailing-zeros factorials prime-number prime-factor
---
Chúng ta sẽ bắt đầu với một bài toán nhỏ như sau:
> Cho một số tự nhiên n, hãy tìm số chữ số 0 liên tiếp cuối cùng của n! (giai thừa)

### 1. Straight-forward
Một cách đơn giản và trực diện nhất, đó chính là brute-force, nhân tất vào, rồi đếm số chữ số 0
```python
def trailing_zeros(n):
  if n == 0:
    return 0
  product = 1
  for i in range(2, n+1):
    product *= i
  tz = 0
  while product%10 == 0:
    tz += 1
    product /= 10
  return tz
```
Đơn giản, dễ hiểu, nhưng rõ ràng là không hiệu quả về performance. Nếu bạn đã từng tham dự vào các cuộc thi **Competitive Programming** thì sẽ biết các cách giải kiểu brute-force này chắc chắn sẽ bị đánh giá là *time-out*!

### 2. Dùng cái đầu
Ta có nhận xét như sau: trailing_zeros(n) chính là số lần mà 10 xuất hiện trong khai triển $$n!$$

Tuy nhiên, $$10 = 2*5$$, nên bài toán sẽ đưa về là tìm số lần xuất hiện của tích giữa 2 và 5 trong $$n!$$. Thêm nữa $$5 > 2$$, ta chắc chắn một điều là số lượng 5 xuất hiện ít hơn số lượng 2 xuất hiện, do đó cuối cùng ta chỉ cần đếm số lần 5 xuất hiện là đủ.

Đếm như thế nào ? Ta có thể nhẩm tính thế này: 5 xuất hiện trong 5, 10, 15, 20, 25, 30... nên tần suất của 5 sẽ là $$\lfloor\frac{n}{5}\rfloor$$ (ở đây là làm tròn xuống, tức floor).

Tuy nhiên như vậy là chưa đủ! Ta thấy 25 = 5*5, nghĩa là trong 25 xuất hiện 2 lần thừa số 5, tương tự với 50, 75, 100, nghĩa là ta cần phải tính thêm một lần xuất hiện của 5 tại đây, tức $$\lfloor\frac{n}{25}\rfloor$$

Tương tự với 125, 625... ta đều phải tính thêm số lần 5 xuất hiện. Tổng quát lên, ta có công thức như sau:
\\[
f(n) = \sum_1^k\lfloor\frac{n}{5^i}\rfloor = \lfloor\frac{n}{5}\rfloor + \lfloor\frac{n}{5^2}\rfloor + \lfloor\frac{n}{5^{3}}\rfloor + ... + \lfloor\frac{n}{5^k}\rfloor
\\]
Với $$5^{k} \leq n <5^{k+1}$$, hay nói cách khác $$k = \lfloor\log_5{n}\rfloor$$

Ví dụ: Tìm số lượng số chữ số 0 liên tiếp cuối cùng của 151!
- Vòng 1: $$\lfloor\frac{151}{5}\rfloor = 30$$
- Vòng 2: $$\lfloor\frac{151}{25}\rfloor = 6$$
- Vòng 3: $$\lfloor\frac{151}{125}\rfloor = 1$$

Vậy có tất cả $$30 + 6 + 1 = 37$$ số chữ số 0

Thuật toán ok rồi, code thôi:
```python
def trailing_zeros(n):
  if n == 0:
    return 0
  k = 1
  tz = 0
  while 5**k <= n:
    tz += n/5**k
    k += 1
  return tz
```

### 3. Mở rộng
Bài toán sẽ ra sao nếu ta thêm một điều kiện nữa, đó là *tìm số chữ số 0 liên tiếp cuối cùng của n trong cơ số $$b\ (b \geq 2)$$ bất kì*? [Khó - Nam Cường](http://mp3.zing.vn/bai-hat/Kho-Nam-Cuong/ZWZADE90.html)

Ta lại có một nhận xét mở rộng của nhận xét trên: số chữ số 0 liên tiếp cuối cùng của một số trong cơ số b chính là số lần b xuất hiện trong khai triển $$n!$$

Đếm như thế nào ? Việc đầu tiên, cũng giống như trên, nhưng tổng quát hóa lên, ta phải phân tích b ra thành *tích của các thừa số nguyên tố*. Và đếm số lần xuất hiện của thừa số có số lần xuất hiện **ít nhất**.

Ví dụ: Tìm số lượng chữ số 0 liên tiếp cuối cùng của 151! trong cơ số 12

Ta có $$12 = 2^2 * 3$$. Khoan, có số mũ, vậy đếm sao? Rất đơn giản, ta vẫn tính số lượng thừa số 2 xuất hiện như bình thường (không mũ). Số lượng đó là:
\\[
\sum_1^k\lfloor\frac{151}{2^i}\rfloor = \sum_1^7\lfloor\frac{151}{2^i}\rfloor = 146
\\]

ta sẽ có một dãy gồm 146 số 2, nhân 2 số 2 lại với nhau, ta sẽ có dãy gồm 73 số $$2^2$$, và đó cũng chính là số lượng thừa số $$2^2$$ trong $$151!$$

Số lượng thừa số 3 là:
\\[
\sum_1^k\lfloor\frac{151}{3^i}\rfloor = \sum_1^4\lfloor\frac{151}{3^i}\rfloor = 72
\\]

$$72 < 73$$, vậy $$151!$$ có 72 chữ số 0 liên tiếp cuối cùng trong cơ số 12

Một cách tổng quát thì số lượng chữ số 0 liên tiếp cuối cùng trong cơ số b của n sẽ là:
\\[
f(n, b) = \min g(n, b_i^{p_i})
\\]

Trong đó:
\\[
b = \prod_{i=1}b_i^{p_i}
\\]
 là phân tích ra thừa số nguyên tố của b.
\\[
g(n, b_i^{p_i}) = \lfloor\frac{\sum_{j=1}\lfloor\frac{n}{b_i^j}\rfloor}{p_i}\rfloor
\\]
là hàm đếm số lượng xuất hiện của thừa số có mũ $$ b_i^{p_i}$$

Code:
```python
import math

def factors(base):
    out = []
    i=2
    while i<=math.sqrt(base):
        if base%i==0:
            base = base/i
            out.append(i)
        else:
            i+=1
    if base>1:
        out.append(base)
    return out


def trailing_zeros(number, base):
    _factors = factors(base)
    data = {}
    for factor in set(_factors):    
        tmp = number
        res = 0
        while tmp:
            tmp = tmp/factor
            res += tmp
        data[factor] = res
    return min([data[factor]/_factors.count(factor) for factor in set(_factors)])
```

### 4. Kết luận
Một bài toán tưởng chừng như đơn giản sẽ trở nên rất thú vị nếu ta biết cách khai thác và nhìn nó dưới những góc độ khác nhau.

Cuộc sống cũng vậy, những điều lớn lao bao giờ cũng bắt nguồn từ những thứ đơn giản nhất.

Enjoy coding!

### 5. Tham khảo
- [Trailing Zero](https://en.wikipedia.org/wiki/Trailing_zero)
- [Legendre's formula](https://en.wikipedia.org/wiki/Legendre%27s_formula)
- [Factorials and Trailing zeros](http://www.purplemath.com/modules/factzero.htm)
