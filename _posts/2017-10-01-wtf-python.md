---
layout: post
title: "Có người nói họ pro Python, tôi cho họ xem bài này, và cái kết 😱"
tags: python
---

Chào các bạn, dạo này mình mới học được cách giật tít từ mấy trang lá cải, đem áp dụng vào một số nơi và thấy là độ hiệu quả không ngờ =))) 

---
Chuyện cũng chẳng có gì, mình có thói quen hay lên github xem mấy trending repositories. Và một ngày đẹp trời mình gặp wtfpython, thấy rất **chất**, nhiều điều mới và hữu ích nên muốn chia sẻ lại cho mọi người. 

Bài khá dài, thực ra là quá dài, nên mình trích những điểm mà mình cho là hay nhất để viết lại (tất nhiên phù hợp với license của tác giả).

Bài viết gốc nằm trên repo [wtfpython](https://github.com/satwikkansal/wtfpython), các bạn có thể lên đó star hay clone về nghiên cứu.

---

## Skipping lines?

#### Output

```py
>>> apple = 11
>>> аpple = 12
>>> apple
11
```

wtf ? có gì đó sai sai ?
#### 💡 Giải thích
Thực chất trong ví dụ trên, hai ký tự 'а' và 'a' là khác nhau. Ký tự 'a' ở dòng 1 là Latin thông thường, và ký tự 'а' ở dòng thứ 2 là [Cyrillic](https://en.wikipedia.org/wiki/Cyrillic_script_in_Unicode) 'а'.
```python
>>> ord('a')
97
>>> ord('а')
1072
```
Thật là kỳ diệu đúng không ? Ví dụ đầu tiên này không liên quan đến Python cho lắm, đơn thuần nó chỉ muốn nhấn mạnh một điều rằng "những đoạn code tưởng chừng như không thể sai vẫn có thể sai".

Sẽ ra sao nếu bạn phải debug một đoạn code mà dev trước đó 'cố ý' để lại như vậy ? 

Sẽ ra sao nếu bạn bị dẫn tới trang fishing bằng cách thức như vậy? ví dụ 'https://аpple.com' ? các bạn có thể đọc thêm về vụ fishing này [tại đây](https://www.forbes.com/sites/leemathews/2017/04/21/this-apple-phishing-site-is-as-sneaky-as-they-come/#cd745a160e18)

Lời khuyên là hãy luôn cẩn thận, vậy thôi.

---

## Time for some hash brownies!
```python
some_dict = {}
some_dict[5.5] = "Ruby"
some_dict[5.0] = "JavaScript"
some_dict[5] = "Python"
```
#### Output
```python
>>> some_dict[5.5]
"Ruby"
>>> some_dict[5.0]
"Python"
>>> some_dict[5]
"Python"
```
"Javascript" đã biến đâu mất rồi?
#### 💡 Giải thích
- Python dictionaries sẽ so sánh xem 2 key có là một hay không bằng cách so sánh giá trị **hash** của chúng
- Với những immutable objects thì nếu cũng có giá trị bằng nhau sẽ cho hash như nhau trong Python
```python
>>> 5 == 5.0
True
>>> hash(5) == hash(5.0)
True
```
Bạn có thể đọc thêm về **hash** trong Python [tại đây](https://stackoverflow.com/questions/32209155/why-can-a-floating-point-dictionary-key-overwrite-an-integer-key-with-the-same-v/32211042#32211042)

---

## Evaluation time discrepancy
```python
array = [1, 8, 15]
g = (x for x in array if array.count(x) > 0)
array = [2, 8, 22]
```
#### Output
```python
print(list(g))
[8]
```
#### 💡 Giải thích
- Trong biểu thức [generator](https://wiki.python.org/moin/Generators) thì phép **in** được thực thi tại lúc khai báo, còn **if** lại được thực thi tại runtime. Vì thế khi x bị khai báo lại là [2, 8, 22] và ta gọi tới g trong `print(list(g))` thì chỉ còn 8 là có count > 0 mà thôi. Vi diệu!

---

## Modifying a dictionary while iterating over it
```python
x = {0: None}

for i in x:
    del x[i]
    x[i+1] = None
    print(i)
```
#### Output
```python
0
1
2
3
4
5
6
7
```
Tại sao in có đúng 8 số ?
#### 💡 Giải thích
- Python không support việc vừa duyệt vừa edit trên một dictionary
- Việc chỉ chạy đúng 8 lần là do cơ chế resize để lưu trữ key trong dictionary. Min size của dictionary sẽ là 8.
- Bạn có thể tìm hiểu thêm [tại đây](https://stackoverflow.com/questions/44763802/modifying-a-dictionary-while-iterating-over-it-bug-in-python-dict)

---

## Deleting a list item while iterating over it
```python
list_1 = [1, 2, 3, 4]
list_2 = [1, 2, 3, 4]
list_3 = [1, 2, 3, 4]
list_4 = [1, 2, 3, 4]

for idx, item in enumerate(list_1):
    del item

for idx, item in enumerate(list_2):
    list_2.remove(item)

for idx, item in enumerate(list_3[:]):
    list_3.remove(item)

for idx, item in enumerate(list_4):
    list_4.pop(idx)
```
#### Output
```python
>>> list_1
[1, 2, 3, 4]
>>> list_2
[2, 4]
>>> list_3
[]
>>> list_4
[2, 4]
```
Tại sao kết quả lại ra [2, 4]
#### 💡 Giải thích
- Phép toán slice sẽ sinh ra object mới chứ không phải là list object lúc đầu nữa:
```python
>>> some_list = [1, 2, 3, 4]
>>> id(some_list)
139798789457608
>>> id(some_list[:]) # Notice that python creates new object for sliced list.
139798779601192
```

Sự khác nhau giữa **del**, **remove**, **pop**

- **del var_name** chỉ đơn thuần là xóa binding của *var_name* khỏi local hay global namespace, nó không ảnh hưởng tới dữ liệu thực tế của list.
- **remove** xóa phần tử đầu tiên tìm thấy được trong list, nếu không tìm thấy thì trả raise *ValueError*.
- **pop** sẽ xóa phần tử tại vị trí xác định trong list, nếu list rỗng thì raise *IndexError*.

**Kết quả**
- Khi duyệt qua các phần tử của dãy bằng index, đầu tiên ta remove 1 (idx=0) khỏi list_2, list_4. Khi này list sẽ trở thành [2,3,4] và idx=0 sẽ trỏ vào 2. Tiếp tục ta sẽ remove 3 (idx=1) ra khỏi list, và list trở thành [2, 4], hết.
- Tại list_1 không có gì thay đổi.
- Tại list_3 sẽ xóa tất cả, vì các phần tử xóa được lấy từ dãy copy.

---

## Let's make a giant string!
```python
def add_string_with_plus(iters):
    s = ""
    for i in range(iters):
        s += "xyz"
    assert len(s) == 3*iters

def add_string_with_format(iters):
    fs = "{}"*iters
    s = fs.format(*(["xyz"]*iters))
    assert len(s) == 3*iters

def add_string_with_join(iters):
    l = []
    for i in range(iters):
        l.append("xyz")
    s = "".join(l)
    assert len(s) == 3*iters

def convert_list_to_string(l, iters):
    s = "".join(l)
    assert len(s) == 3*iters
```
#### Output
```python
>>> timeit(add_string_with_plus(10000))
100 loops, best of 3: 9.73 ms per loop
>>> timeit(add_string_with_format(10000))
100 loops, best of 3: 5.47 ms per loop
>>> timeit(add_string_with_join(10000))
100 loops, best of 3: 10.1 ms per loop
>>> l = ["xyz"]*10000
>>> timeit(convert_list_to_string(l, 10000))
10000 loops, best of 3: 75.3 µs per loop
```
Có điều gì đáng chú ý với các cách concat strings này ?
#### Giải thích
- Đừng bao giờ sử dụng `+` để nối string. Nếu bạn còn đang dùng như vậy, hãy chuyển qua dùng `.format` hay `%`. Bởi vì *str* trong Python là *immutable*, nghĩ là mỗi khi bạn ghép 2 string lại với nhau, nó sẽ được copy sang một string mới. Thật tệ hại nếu số lượng strings là rất lớn.
- Nếu mà các strings của bạn ở sẵn dạng iterable như list, thì dùng `''.join(iterable_object)` sẽ nhanh hơn rất nhiều.

---

## String interning
#### Output
```python
>>> a = "some_string"
>>> id(a)
140420665652016
>>> id("some" + "_" + "string") # Notice that both the ids are same.
140420665652016
# using "+", three strings:
>>> timeit.timeit("s1 = s1 + s2 + s3", setup="s1 = ' ' * 100000; s2 = ' ' * 100000; s3 = ' ' * 100000", number=100)
0.25748300552368164
# using "+=", three strings:
>>> timeit.timeit("s1 += s2 + s3", setup="s1 = ' ' * 100000; s2 = ' ' * 100000; s3 = ' ' * 100000", number=100)
0.012188911437988281
```
#### Giải thích
- `+=` nhanh hơn `+` khi concat các string vì string đầu tiên không bị hủy khi tính toán (ví dụ s1 trong s1 = s1 + s2 + s3).
- Kết quả cuối cùng sẽ vẫn là object s1 (id như cũ)

---

## Yes, it exists!
Có thể bạn đã biết, Python cũng có `else` cho vòng lặp `for`:
```python
def does_exists_num(l, to_find):
    for num in l:
        if num == to_find:
            print("Exists!")
            break
    else:
        print("Does not exist")
```
#### Output
```python
>>> some_list = [1, 2, 3, 4, 5]
>>> does_exists_num(some_list, 4)
Exists!
>>> does_exists_num(some_list, -1)
Does not exist
```

thậm chí trong `exception` cũng có `else` luôn:
```python
try:
    pass
except:
    print("Exception occurred!!!")
else:
    print("Try block executed successfully...")
```
#### Output
```python
Try block executed successfully...
```
#### Giải thích
- `else` trong vòng lặp for sẽ được thực thi nếu không có `break` xảy ra tại tất cả các vòng lặp
- `else` sau `try` block được gọi là *completion clause*, nghĩa là nếu try thành công thì else sẽ được gọi

---

## `is` is not what it is!
Đoạn code sau đã từng gây sốt cộng đồng mạng
```python
>>> a = 256
>>> b = 256
>>> a is b
True

>>> a = 257
>>> b = 257
>>> a is b
False

>>> a = 257; b = 257
>>> a is b
True
```
tạm bỏ qua cái cộng đồng suốt ngày đau yếu và dễ lên cơn, đoạn code trên thực sự **CHẤT!**.
#### Giải thích
**Sự khác nhau giữa `is` và `==`:**
- `is` đúng khi 2 object là một, nghĩa là cùng id
- `==` đúng khi *giá trị* của 2 object là giống nhau

```python
>>> [] == []
True
>>> [] is [] # These are two empty lists at two different memory locations.
False
```

**256 is an existing object but 257 isn't**
- Khi bạn start Python lên, thì các giá trị từ -5 cho tới 256 đều đã được cấp phát bộ nhớ rồi. Sở dĩ có điều này vì đây là các giá trị thường dùng nhất trong Python, nên để tiện cho người sử dụng, Python đã "chuẩn bị" trước cho chúng ta. Vì thế khi ta khai báo một giá trị từ -5 tới 256, thực chất cái ta nhận được là một tham chiếu đến object đã tồn tại rồi. 
- 257 rất tốt, nhưng Python rất tiếc.
```python
>>> id(256)
10922528
>>> a = 256
>>> b = 256
>>> id(a)
10922528
>>> id(b)
10922528
>>> id(257)
140084850247312
>>> x = 257
>>> y = 257
>>> id(x)
140084850247440
>>> id(y)
140084850247344
```
- Khi khai a và b bằng 257 trên cùng một dòng, thì trình dịch Python sẽ tạo một object, và tham chiếu biến thứ 2 đến object đó cùng lúc. Do đó khi này `a is b` là **True**. Nếu không khai báo trên một dòng, Python không biết được điều này, và kết quả là **False**.

---

## `is not ...` is not `is (not ...)`
```python
>>> 'something' is not None
True
>>> 'something' is (not None)
False
```
#### Giải thích
- `is not` là **một phép toán** chứ không phải hai phép toán, nên rõ ràng nó khác `is (not ...)` rồi

---

## The function inside loop sticks to the same output
```python
funcs = []
results = []
for x in range(7):
    def some_func():
        return x
    funcs.append(some_func)
    results.append(some_func())

funcs_results = [func() for func in funcs]
```
#### Output
```python
>>> results
[0, 1, 2, 3, 4, 5, 6]
>>> funcs_results
[6, 6, 6, 6, 6, 6, 6]
```
Sao func_results lại không phải trả về [0, 1, 2, 3, 4, 5, 6] như results?
#### Giải thích
- Khi định nghĩa một function trong một vòng lặp, biến x trỏ đến x ngoài function, thì nó chỉ trỏ đến bản thân biến x, chứ không phải giá trị của x. Theo đó tất cả các function sẽ sử dụng giá trị cuối cùng được gán để tính toán.
- Để function trên hoạt động theo mong muốn, tức trả về [0, 1, 2, 3, 4, 5, 6], ta sẽ cần một trick nhỏ, đó là thêm keyword parameter để thay đổi scope của biến về scope của function.
```python
funcs = []
for x in range(7):
    def some_func(x=x):
        return x
    funcs.append(some_func)
```
#### Output
```python
>>> funcs_results = [func() for func in funcs]
>>> funcs_results
[0, 1, 2, 3, 4, 5, 6]
```

---

## Loop variables leaking out of local scope!
1.
```python
for x in range(7):
    if x == 6:
        print(x, ': for x inside loop')
print(x, ': x in global')
```
#### Output
```python
6 : for x inside loop
6 : x in global
```
Nhưng x còn chưa được định nghĩa cơ mà!

2.
```python
# This time let's initialize x first
x = -1
for x in range(7):
    if x == 6:
        print(x, ': for x inside loop')
print(x, ': x in global')
```
#### Output
```python
6 : for x inside loop
6 : x in global
```

3.
```python
x = 1
print([x for x in range(5)])
print(x, ': x in global')
```

#### Output (on Python 2.x):
```python
[0, 1, 2, 3, 4]
(4, ': x in global')
```

#### Output (on Python 3.x):
```python
[0, 1, 2, 3, 4]
1 : x in global
```

#### Giải thích
- Trong Python thì for-loop sẽ để lại các biến mà nó đã định nghĩa trong vòng lặp với giá trị cuối cùng khi vòng lặp kết thúc. Nếu như ta định nghĩa biến ở ngoài vòng lặp, thì nó cũng sẽ được gán lại giá trị trong vòng lặp đó.
- Scope của biến trong list comprehension đã được thay đổi giữa Python 2.0 và 3.0. Tại Python 2.0 thì x sẽ bị ảnh hưởng bởi `print([x for x in range(5)])`, còn Python 3.0 thì không. Bạn có thể tham khảo thêm [tại đây](https://docs.python.org/3/whatsnew/3.0.html)
> "List comprehensions no longer support the syntactic form [... for var in item1, item2, ...]. Use [... for var in (item1, item2, ...)] instead. Also, note that list comprehensions have different semantics: they are closer to syntactic sugar for a generator expression inside a list() constructor, and in particular the loop control variables are no longer leaked into the surrounding scope."

---

###  A tic-tac-toe where X wins in the first attempt!

```py
row = [""]*3
board = [row]*3
```

**Output:**
```py
>>> board
[['', '', ''], ['', '', ''], ['', '', '']]
>>> board[0]
['', '', '']
>>> board[0][0]
''
>>> board[0][0] = "X"
>>> board
[['X', '', ''], ['X', '', ''], ['X', '', '']]
```

Tại sao 3 giá trị đầu đều là ếch ?

#### 💡 Giải thích:

Đầu tiên khi ta khai báo biến `row` sẽ trông thế này
![image]({{ site.url }}/assets/images/after_row_initialized.png)
Khi ta nhân nhiều biến `row` vào với nhau, mục đích để tạo nên board 3x3, nhưng thực chất chúng vẫn đều trỏ đến `row` mà thôi.
![image]({{ site.url }}/assets/images/after_board_initialized.png)
Nếu muốn tạo board, hãy dùng list comprehension thay thế:
```py
board = [[""]*3 for i in range(3)]
```

---

###  Beware of default mutable arguments!

```py
def some_func(default_arg=[]):
    default_arg.append("some_string")
    return default_arg
```

**Output:**
```py
>>> some_func()
['some_string']
>>> some_func()
['some_string', 'some_string']
>>> some_func([])
['some_string']
>>> some_func()
['some_string', 'some_string', 'some_string']
```

#### 💡 Giải thích:

- Chúng ta luôn phải cẩn thận với các giá trị *mutable* trong Python (list chẳng hạn). Giá trị default của tham số `default_arg` là mutable, tức [], sẽ không được khởi tạo mỗi khi gọi hàm, mà nó sẽ được gán bằng giá trị sử dụng lần gần nhất.

    ```py
    def some_func(default_arg=[]):
        default_arg.append("some_string")
        return default_arg
    ```

    **Output:**
    ```py
    >>> some_func.__defaults__ #This will show the default argument values for the function
    ([],)
    >>> some_func()
    >>> some_func.__defaults__
    (['some_string'],)
    >>> some_func()
    >>> some_func.__defaults__
    (['some_string', 'some_string'],)
    >>> some_func([])
    >>> some_func.__defaults__
    (['some_string', 'some_string'],)
    ```

- Một cách thường được sử dụng để tránh lỗi với tham số mutable đấy chính là gán cho nó giá trị `None` và mỗi lần gọi hàm thì check xem tham số đã được khởi tạo chưa, nếu chưa thì khởi tạo nó.

    ```py
    def some_func(default_arg=None):
        if not default_arg:
            default_arg = []
        default_arg.append("some_string")
        return default_arg
    ```

---

### Same operands, different story!

1\.
```py
a = [1, 2, 3, 4]
b = a
a = a + [5, 6, 7, 8]
```

**Output:**
```py
>>> a
[1, 2, 3, 4, 5, 6, 7, 8]
>>> b
[1, 2, 3, 4]
```

2\.
```py
a = [1, 2, 3, 4]
b = a
a += [5, 6, 7, 8]
```

**Output:**
```py
>>> a
[1, 2, 3, 4, 5, 6, 7, 8]
>>> b
[1, 2, 3, 4, 5, 6, 7, 8]
```

#### 💡 Giải thích:

*  `a += b` không giống với `a = a + b`

* `a = a + [5,6,7,8]` tạo ra object mới và trỏ a tới nó, b sẽ vẫn trỏ tới giá trị a cũ.

* `a + =[5,6,7,8]` chỉ mở rộng a mà không thay đổi địa chỉ của a, b vẫn trỏ tới giá trị giống như a.

---

###  Using a variable not defined in scope

```py
a = 1
def some_func():
    return a

def another_func():
    a += 1
    return a
```

**Output:**
```py
>>> some_func()
1
>>> another_func()
UnboundLocalError: local variable 'a' referenced before assignment
```

#### 💡 Giải thích:
* Khi một phép gán xảy ra, nó sẽ trở thành biến local trong scope đó. Theo đó `a` sẽ là biến local trong `another_func`, nó chưa được khai báo nên error được throw.
* Bạn có thể đọc thêm về scope của biến trong Python [tại đây](http://sebastianraschka.com/Articles/2014_python_scope_and_namespaces.html) 
* Nếu muốn sử dụng `a` trong `another_func` thì ta sẽ phải thêm keyword `global` vào như dưới đây:
  ```py
  def another_func()
      global a
      a += 1
      return a
  ```
  
  **Output:**
  ```py
  >>> another_func()
  2
  ```

---

###  When True is actually False

```py
True = False
if True == False:
    print("I've lost faith in truth!")
```

**Output:**
```py
I've lost faith in truth!
```
True == False ? thật giả lẫn lộn hết rồi.
#### 💡 Giải thích:

- Ban đầu khi thiết kế Python, không hề có kiểu dữ liệu `bool`, người ta dùng 0 đại diện cho False, và dùng giá trị khác 0 đại diện cho True. Sau này khi thêm vào `True`, `False`, và kiểu `bool`, để tương thích với phiên bản cũ, người ta để `True` và `False` là biến chứ không phải là hằng số.
- Python3 là phiên bản hoàn toàn mới và không tương thích với phiên bản cũ, nên ví dụ trên sẽ không chạy được trên Python3.

---

###  Name resolution ignoring class scope

1\.
```py
x = 5
class SomeClass:
    x = 17
    y = (x for i in range(10))
```

**Output:**
```py
>>> list(SomeClass.y)[0]
5
```

2\.
```py
x = 5
class SomeClass:
    x = 17
    y = [x for i in range(10)]
```

**Output (Python 2.x):**
```py
>>> SomeClass.y[0]
17
```

**Output (Python 3.x):**
```py
>>> SomeClass.y[0]
5
```

#### 💡 Giải thích
- generator có scope riêng của nó, nên x trong ví dụ đầu trong generator sẽ không bị ảnh hưởng bởi x local.
- từ Python 3.X list comprehensions cũng có scope riêng của nó, Python2 thì không.

---
###  From filled to None in one instruction...

```py
some_list = [1, 2, 3]
some_dict = {
  "key_1": 1,
  "key_2": 2,
  "key_3": 3
}

some_list = some_list.append(4)
some_dict = some_dict.update({"key_4": 4})
```

**Output:**
```py
>>> print(some_list)
None
>>> print(some_dict)
None
```

#### 💡 Giải thích

- Hãy cẩn thận với các thay đổi in-place như `append` hay `update` hay `sort`... kết quả trả về là `None` chứ không phải bản thân object đó. Vì thế phép gán ở đây là không cần thiết, thật tai hại.

---

###  Class attributes and instance attributes

1\.
```py
class A:
    x = 1

class B(A):
    pass

class C(A):
    pass
```

**Ouptut:**
```py
>>> A.x, B.x, C.x
(1, 1, 1)
>>> B.x = 2
>>> A.x, B.x, C.x
(1, 2, 1)
>>> A.x = 3
>>> A.x, B.x, C.x
(3, 2, 3)
>>> a = A()
>>> a.x, A.x
(3, 3)
>>> a.x += 1
>>> a.x, A.x
(4, 3)
```

2\.
```py
class SomeClass:
    some_var = 15
    some_list = [5]
    another_list = [5]
    def __init__(self, x):
        self.some_var = x + 1
        self.some_list = self.some_list + [x]
        self.another_list += [x]
```

**Output:**

```py
>>> some_obj = SomeClass(420)
>>> some_obj.some_list
[5, 420]
>>> some_obj.another_list
[5, 420]
>>> another_obj = SomeClass(111)
>>> another_obj.some_list
[5, 111]
>>> another_obj.another_list
[5, 420, 111]
>>> another_obj.another_list is SomeClass.another_list
True
>>> another_obj.another_list is some_obj.another_list
True
```


#### 💡 Giải thích:

* Biến trong class và biến trong instance của class được quản lý giống như dictionary. Mỗi khi một biến không tìm thấy trong từ điển của lớp hiện tại, nó sẽ tìm kiếm và trả về giá trị từ lớp cha.

---

### Midnight time doesn't exist?

```py
from datetime import datetime

midnight = datetime(2018, 1, 1, 0, 0)
midnight_time = midnight.time()

noon = datetime(2018, 1, 1, 12, 0)
noon_time = noon.time()

if midnight_time:
    print("Time at midnight is", midnight_time)

if noon_time:
    print("Time at noon is", noon_time)
```

**Output:**
```sh
('Time at noon is', datetime.time(12, 0))
```
thời gian lúc nửa đêm đâu mất rồi ? phải chăng Python đi ngủ ?

#### 💡 Giải thích:

Trước phiên bản Python 3.5, giá trị `datetime.time` sẽ bị coi là `False` nếu nó gọi đến thời gian nửa đêm (thời gian UTC). Nghe khá là creepy^^

---

### What's wrong with booleans?

1\.
```py
# A simple example to count the number of boolean and
# integers in an iterable of mixed data types.
mixed_list = [False, 1.0, "some_string", 3, True, [], False]
integers_found_so_far = 0
booleans_found_so_far = 0

for item in mixed_list:
    if isinstance(item, int):
        integers_found_so_far += 1
    elif isinstance(item, bool):
        booleans_found_so_far += 1
```

**Outuput:**
```py
>>> booleans_found_so_far
0
>>> integers_found_so_far
4
```

2\.
```py
another_dict = {}
another_dict[True] = "JavaScript"
another_dict[1] = "Ruby"
another_dict[1.0] = "Python"
```

**Output:**
```py
>>> another_dict[True]
"Python"
```


#### 💡 Giải thích:

* `bool` là subclass của `int`
  ```py
  >>> isinstance(True, int)
  True
  >>> isinstance(False, int)
  True
  ```

* Giá trị của `True` là `1`, và `False` là `0`.
  ```py
  >>> True == 1 == 1.0 and False == 0 == 0.0
  True
  ```
## Conclusion
Cái này chúng tôi gọi là Python **chất** đến từng dòng code!

## References
- [wtfpython](https://github.com/satwikkansal/wtfpython)