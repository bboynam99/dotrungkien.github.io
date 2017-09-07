---
layout: post
title:  "Random() có thực sự ngẫu nhiên ?"
mathjax: true
tags: random algorithm prng
---
Không, trên đời chẳng có gì là ngẫu nhiên cả!

Tất cả đều là những quy luật được ẩn giấu, chỉ là chúng ta có phát hiện hay hiểu được nó hay không mà thôi; bởi đôi khi nó đơn giản, đôi khi phức tạp, thậm chí siêu phức tạp.

Random() cũng vậy. Bản thân máy tính do con người tạo ra, nên những quy tắc hoạt động của nó đều dựa trên những gì mà con người thiết kế. Tuỳ vào các hệ thống khác nhau mà random sẽ được thiết kế với các thuật toán khác nhau, nhưng ít nhất cần đảm bảo được các tiêu chí sau:
1. Tính ngẫu nhiên của kết quả tạo ra
2. Tính bảo mật của thuật toán

Quá trình sinh ra random trong máy tính được gọi là **Pseudo Random Number Generation** (PRNG).

### RPNG Concept
Ta sẽ có 3 khái niệm chung trong PRNG:
- *Seed*: có thể coi như là giá trị khởi tạo của PRNG.
- *Internal State*: trạng thái của PRNG, bao gồm các biến số để có thể dự đoán được giá trị tiếp theo và trạng thái tiếp theo của PRNG.
- *Period*: chu kỳ của random, random sẽ lặp lại mỗi khi chu kỳ kết thúc.

### Random Distribution Types
Tuỳ vào mục đích sử dụng, Random() trong các ngôn ngữ lập trình sẽ sinh ra các số ngẫu nhiên theo *phân phối đều* (Uniform Distribution) với xác suất xuất hiện các số là gần như nhau
![Uniform Distribution](http://users.ecs.soton.ac.uk/jn2/simulation/unif.png)
hoặc *phân phối chuẩn* (Normal Distribution) để sinh ra các giá trị xung quanh một giá trị nào đó.
![Normal Distribution](http://users.ecs.soton.ac.uk/jn2/simulation/normal.png)
Dưới đây là list các random function và phân phối tạo ra tương ứng trong một số ngôn ngữ lập trình thông dụng
```python
# C#
  Uniform: Random.NextDouble
  Normal: NA
# C/C++
  Uniform: rand
  Normal: NA
# Java
  Uniform: Random.NextDouble
  Normal: Random.nextGaussian
# Python
  Uniform: random.random
  Normal: random.randn
```
Đó, rõ ràng Random() không phải là dùng tuỳ tiện được mà phải phụ thuộc vào việc vào chúng ta muốn dữ liệu như thế nào nữa.

### PRNG Algorithms
Có rất nhiều thuật toán trong PRNG, các bạn có thể tham khảo thêm [tại đây](https://en.wikipedia.org/wiki/List_of_random_number_generators)
Trong bài này mình sẽ giới thiệu qua 3 thuật toán được sử dụng trong các ngôn ngữ lập trình.
#### Linear Congruential Generator (LCG)
Đây là thuật toán sinh ngẫu nhiên cổ điển nhất và phổ biến nhất được sử dụng trong PRNG. LCG là thuật toán built-in trong C/C++, Java và C#.

LCG rất đơn giản, rất trực quan và dễ hiểu, sử dụng chỉ một hàm:
\\[
X_{n+1} = (aX_{n} + c)\ mod\ m
\\]
Trong đó:
- $$m,\ 0 < m$$: Mô đun, thường sẽ là một số đủ lớn, ví dụ $$2^{32}, 2^{31} - 1, 2^{48}, 2^{64}$$
- $$a,\ 0 < a < m$$: Hằng số nhân *mutiplier*
- $$c,\ 0 <= c < m$$: Hằng số cộng thêm *increment*
- $$X_{0},\ 0 <= X_{0} < m$$: seed, giá trị khởi tạo

Chu kỳ của LCG lớn nhất là m, và để LCG sinh ra tất cả các giá trị trong chu kỳ với mọi giá trị khởi tạo thì sẽ cần những điều kiện ràng buộc như sau
- $$m$$ và $$c$$ là nguyên tố cùng nhau
- $$a-1$$ chia hết cho mọi thừa số nguyên tố của $$m$$
- $$a-1$$ chia hết cho 4 nếu $$m$$ chia hết cho 4

Về giá trị của các hằng số, các bạn có thể tham khảo thêm [tại đây](https://en.wikipedia.org/wiki/Linear_congruential_generator)

**Ưu điểm**: Rất nhanh và tốn ít bộ nhớ (32 hoặc 64 bits)

**Nhược điểm**: Tính chất ngẫu nhiên chưa cao, và do đó với những hệ thống thực sự cần độ ngẫu nhiên rất cao, người ta không khuyến khích sử dụng LCG. Và thay vào đó là sử dụng *Mersenne Twister* (sẽ được nói tới trong phần sau).

#### Multiply with Carry (MWC)
Để tạo ra chu kỳ random lớn hơn, [George Marsaglia](https://en.wikipedia.org/wiki/George_Marsaglia) đã tạo ra một thuật toán PRNG khác với tên gọi Multiply with Carry (MWC). Trong MWC thì ta sẽ dùng một set gồm từ hai cho tới hàng ngàn giá trị cho seed.

Và chu kỳ của MWC cũng rất lớn, từ $$2^{60}$$ cho tới $$2^{2000000}$$, nghĩa là lớn hơn rất rất nhiều so với LCG.

Trong MWC chúng ta sẽ có một giá trị **r**, gọi là *lag* của MWC. Và cũng giống như LCG, chúng ta cũng sẽ có *mutiplier* và *modulus*, nhưng sẽ không còn *increment*, mà thay vào đó là một giá trị *carry*. Công thức sẽ như sau:
\\[
x_{n} = (ax_{n-r} + c_{n-1})\ \ mod \ \ b,\ n>=r
\\]
Trong đó, cũng giống như trên, **a** sẽ là mutiplier, và ở đây **b** sẽ là mô đun, thường là $$2^{32}$$. Điểm khác biệt là giá trị carry **c**, giá trị này sẽ được dùng để tính toán giá trị **x** tiếp theo. Công thức của **c** là:
\\[
c_{n} = \lfloor \frac{ax_{n-r} + c_{n-1}}{b} \rfloor,\ n>=r
\\]
người ta thường chọn giá trị của a sao cho $$ab-1$$ là *Safe Prime*, tức $$ab-1$$ và $$\frac{ab}{2}-1$$ đều là nguyên tố, khi đó chu kì của MWC sẽ là $$\frac{ab}{2}-1$$

Các bạn có thể tham khảo bảng giá trị chu kì của MWC như dưới đây:
![](https://i.gyazo.com/0fbbddd708d8049f04da67a6a05328de.png)

#### Mersenne Twister
*Mersenne Twister* là một thuật toán PRNG được *Makoto Matsumoto* và *Takuji Nishimura* phát triển vào năm 1997. Đây là một thuật toán thực sự tuyệt vời. Rất nhanh và tạo ra được dãy số với chất lượng ngẫu nhiên rất cao.

*Mersenne Twister* được sử dụng như là built-in PRNG cho Ruby, Python và R.

Cái tên *Mersenne Twister* được chọn vì chu kì của số ngẫu nhiên tạo ra bởi thuật toán này luôn là một [số nguyên tố Mersenne](https://en.wikipedia.org/wiki/Mersenne_prime)
> FYI: Số nguyên tố Mersenne có dạng $$M_{n} = 2^{n} - 1$$, ví dụ 31.

Số nguyên tố Mersenne thường được sử dụng trong thuật toán sinh random là $$2^{19937}−1$$, đó cũng là nguồn gốc của cái tên *MT19937* - standard implement sử dụng từ với độ dài 32 bits.
(to be continued....)

### Conclusion
Rõ ràng random() không phải là ngẫu nhiên, đó đều là kết quả do máy tính (hay chính xác hơn là con người) tạo ra mà thôi.
Hãy luôn tin rằng nếu bạn chưa có bạn gái, hay chưa giàu, hay chưa trúng Vietlot, thì chỉ là do *chưa tới lượt* đó mà, dù sớm hay muộn thì may mắn cũng sẽ đến.

Good luck!
<br>
P/S: Mình nghi ngờ ông trời dùng phân phối chuẩn, vì một khi đã giàu thì thường kèm đẹp trai, học giỏi, nhiều gái theo (haiz)
### References
- [Random numbers: generators and distributions](http://users.ecs.soton.ac.uk/jn2/simulation/random.html)
- [Artificial intelligence for humans volume 1](https://www.amazon.com/Artificial-Intelligence-Humans-Fundamental-Algorithms/dp/1493682229)
- [List of random number generators](https://en.wikipedia.org/wiki/List_of_random_number_generators)
- [Multiply with Carry](https://en.wikipedia.org/wiki/Multiply-with-carry)
