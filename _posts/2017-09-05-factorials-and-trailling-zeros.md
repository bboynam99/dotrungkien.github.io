---
layout: post
title:  "Factorials and Trailing Zeros"
mathjax: true
tags: algorithm trailing-zeros factorials prime-number prime-factor
---
### Intro
Chúng ta sẽ bắt đầu với một bài toán nhỏ như sau:
> Cho một số tự nhiên n, hãy tìm số chữ số 0 liên tiếp cuối cùng của n! (giai thừa)

### Straight-forward
Một cách đơn giản và trực diện nhất, đó chính là brute-force, nhân tất vào, rồi đếm số chữ số 0
```python
def ftz(n):
  if n == 0: return 0
  product = 1
  for i in range(n+1): product *= i
  res = 0
  while product%10 == 0:
    res += 1
    product /= 10
  return res
```
Đơn giản, dễ hiểu, nhưng rõ ràng là không hiệu quả. Nếu bạn đã từng tham dự vào các cuộc thi Competitive Programming thì sẽ biết các cách giải kiểu brute-force này chắc chắn sẽ bị đánh giá là *time-out*!

### Dùng cái đầu
Ta có nhận xét như sau:
