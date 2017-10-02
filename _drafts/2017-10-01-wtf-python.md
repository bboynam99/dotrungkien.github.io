---
layout: post
title: "Có người nói họ pro Python, tôi cho họ xem bài này, và cái kết 😱"
tags: python
---

Chào các bạn, dạo này mình mới học được cách giật tít từ mấy trang lá cải, đem áp dụng vào một số nơi và thấy là độ hiệu quả không ngờ =))) 

---
Chuyện cũng chẳng có gì, mình có thói quen hay lên github xem mấy trending repositories. Và một ngày đẹp trời mình gặp wtfpython, thấy rất **chất**, nhiều điều mới và hữu ích nên muốn chia sẻ lại cho mọi người.

Bài viết gốc nằm trên repo [wtfpython](https://github.com/satwikkansal/wtfpython), các bạn có thể lên đó star hay clone về nghiên cứu.

## Skipping lines?

#### Output
```
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

## `is not ...` is not `is (not ...)`
```python
>>> 'something' is not None
True
>>> 'something' is (not None)
False
```
#### Giải thích
- `is not` là **một phép toán** chứ không phải hai phép toán, nên rõ ràng nó khác `is (not ...)` rồi

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

#### 💡 Explanation:

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

#### 💡 Explanation:

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

#### 💡 Explanation:

*  `a += b` không giống với `a = a + b`

* `a = a + [5,6,7,8]` tạo ra object mới và trỏ a tới nó, b sẽ vẫn trỏ tới giá trị a cũ.

* `a + =[5,6,7,8]` chỉ mở rộng a mà không thay đổi địa chỉ của a, b vẫn trỏ tới giá trị giống như a.

---

###  Mutating the immutable!

```py
some_tuple = ("A", "tuple", "with", "values")
another_tuple = ([1, 2], [3, 4], [5, 6])
```

**Output:**
```py
>>> some_tuple[2] = "change this"
TypeError: 'tuple' object does not support item assignment
>>> another_tuple[2].append(1000) #This throws no error
>>> another_tuple
([1, 2], [3, 4], [5, 6, 1000])
>>> another_tuple[2] += [99, 999]
TypeError: 'tuple' object does not support item assignment
>>> another_tuple
([1, 2], [3, 4], [5, 6, 1000, 99, 999])
```

But I thought tuples were immutable...

#### 💡 Explanation:

* Quoting from https://docs.python.org/2/reference/datamodel.html

    > Immutable sequences
        An object of an immutable sequence type cannot change once it is created. (If the object contains references to other objects, these other objects may be mutable and may be modified; however, the collection of objects directly referenced by an immutable object cannot change.)

* `+=` operator changes the list in-place. The item assignment doesn't work, but when the exception occurs, the item has already been changed in place.

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

#### 💡 Explanation:
* When you make an assignment to a variable in a scope, it becomes local to that scope. So `a` becomes local to the scope of `another_func`,  but it has not been initialized previously in the same scope which throws an error.
* Read [this](http://sebastianraschka.com/Articles/2014_python_scope_and_namespaces.html) short but an awesome guide to learn more about how namespaces and scope resolution works in Python.
* To modify the outer scope variable `a` in `another_func`, use `global` keyword.
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

###  The disappearing variable from outer scope

```py
e = 7
try:
    raise Exception()
except Exception as e:
    pass
```

**Output (Python 2.x):**
```py
>>> print(e)
# prints nothing
```

**Output (Python 3.x):**
```py
>>> print(e)
NameError: name 'e' is not defined
```

#### 💡 Explanation:

* Source: https://docs.python.org/3/reference/compound_stmts.html#except

  When an exception has been assigned using `as` target, it is cleared at the end of the except clause. This is as if

  ```py
  except E as N:
      foo
  ```

  was translated to

  ```py
  except E as N:
      try:
          foo
      finally:
          del N
  ```

  This means the exception must be assigned to a different name to be able to refer to it after the except clause. Exceptions are cleared because, with the traceback attached to them, they form a reference cycle with the stack frame, keeping all locals in that frame alive until the next garbage collection occurs.

* The clauses are not scoped in Python. Everything in the example is present in the same scope, and the variable `e` got removed due to the execution of the `except` clause. The same is not the case with functions which have their separate inner-scopes. The example below illustrates this:

     ```py
     def f(x):
         del(x)
         print(x)

     x = 5
     y = [5, 4, 3]
     ```

     **Output:**
     ```py
     >>>f(x)
     UnboundLocalError: local variable 'x' referenced before assignment
     >>>f(y)
     UnboundLocalError: local variable 'x' referenced before assignment
     >>> x
     5
     >>> y
     [5, 4, 3]
     ```

* In Python 2.x the variable name `e` gets assigned to `Exception()` instance, so when you try to print, it prints nothing.

    **Output (Python 2.x):**
    ```py
    >>> e
    Exception()
    >>> print e
    # Nothing is printed!
    ```


---

###  Return return everywhere!

```py
def some_func():
    try:
        return 'from_try'
    finally:
        return 'from_finally'
```

**Output:**
```py
>>> some_func()
'from_finally'
```

#### 💡 Explanation:

- When a `return`, `break` or `continue` statement is executed in the `try` suite of a "try…finally" statement, the `finally` clause is also executed ‘on the way out.
- The return value of a function is determined by the last `return` statement executed. Since the `finally` clause always executes, a `return` statement executed in the `finally` clause will always be the last one executed.

---

###  When True is actually False

```py
True = False
if True == False:
    print("I've lost faith in truth!")
```

**Output:**
```
I've lost faith in truth!
```

#### 💡 Explanation:

- Initially, Python used to have no `bool` type (people used 0 for false and non-zero value like 1 for true). Then they added `True`, `False`, and a `bool` type, but, for backward compatibility, they couldn't make `True` and `False` constants- they just were built-in variables.
- Python 3 was backwards-incompatible, so it was now finally possible to fix that, and so this example won't work with Python 3.x!

---

###  Be careful with chained operations

```py
>>> True is False == False
False
>>> False is False is False
True
>>> 1 > 0 < 1
True
>>> (1 > 0) < 1
False
>>> 1 > (0 < 1)
False
```

#### 💡 Explanation:

As per https://docs.python.org/2/reference/expressions.html#not-in

> Formally, if a, b, c, ..., y, z are expressions and op1, op2, ..., opN are comparison operators, then a op1 b op2 c ... y opN z is equivalent to a op1 b and b op2 c and ... y opN z, except that each expression is evaluated at most once.

While such behavior might seem silly to you in the above examples, it's fantastic with stuff like `a == b == c` and `0 <= x <= 100`.

* `False is False is False` is equivalent to `(False is False) and (False is False)`
* `True is False == False` is equivalent to `True is False and False == False` and since the first part of the statement (`True is False`) evaluates to `False`, the overall expression evaluates to `False`.
* `1 > 0 < 1` is equivalent to `1 > 0 and 0 < 1` which evaluates to `True`.
* The expression `(1 > 0) < 1` is equivalent to `True < 1` and
  ```py
  >>> int(True)
  1
  >>> True + 1 #not relevant for this example, but just for fun
  2
  ```
  So, `1 < 1` evaluates to `False`


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

#### 💡 Explanation
- Scopes nested inside class definition ignore names bound at the class level.
- A generator expression has its own scope.
- Starting from Python 3.X, list comprehensions also have their own scope.

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

#### 💡 Explanation

Most methods that modify the items of sequence/mapping objects like `list.append`, `dict.update`, `list.sort`, etc. modify the objects in-place and return `None`. The rationale behind this is to improve performance by avoiding making a copy of the object if the operation can be done in-place (Referred from [here](http://docs.python.org/2/faq/design.html#why-doesn-t-list-sort-return-the-sorted-list))

---

###  Explicit typecast of strings

This is not a WTF at all, but it took me so long to realize such things existed in Python. So sharing it here for the beginners.

```py
a = float('inf')
b = float('nan')
c = float('-iNf')  #These strings are case-insensitive
d = float('nan')
```

**Output:**
```py
>>> a
inf
>>> b
nan
>>> c
-inf
>>> float('some_other_string')
ValueError: could not convert string to float: some_other_string
>>> a == -c #inf==inf
True
>>> None == None # None==None
True
>>> b == d #but nan!=nan
False
>>> 50/a
0.0
>>> a/a
nan
>>> 23 + b
nan
```

#### 💡 Explanation:

`'inf'` and `'nan'` are special strings (case-insensitive), which when explicitly type casted to `float` type, are used to represent mathematical "infinity" and "not a number" respectively.

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


#### 💡 Explanation:

* Class variables and variables in class instances are internally handled as dictionaries of a class object. If a variable name is not found in the dictionary of the current class, the parent classes are searched for it.
* The `+=` operator modifies the mutable object in-place without creating a new object. So changing the attribute of one instance affects the other instances and the class attribute as well.

---

###  Catching the Exceptions!

```py
some_list = [1, 2, 3]
try:
    # This should raise an ``IndexError``
    print(some_list[4])
except IndexError, ValueError:
    print("Caught!")

try:
    # This should raise a ``ValueError``
    some_list.remove(4)
except IndexError, ValueError:
    print("Caught again!")
```

**Output (Python 2.x):**
```py
Caught!

ValueError: list.remove(x): x not in list
```

**Output (Python 3.x):**
```py
  File "<input>", line 3
    except IndexError, ValueError:
                     ^
SyntaxError: invalid syntax
```

#### 💡 Explanation

* To add multiple Exceptions to the except clause, you need to pass them as parenthesized tuple as the first argument. The second argument is an optional name, which when supplied will bind the Exception instance that has been raised. Example,
  ```py
  some_list = [1, 2, 3]
  try:
     # This should raise a ``ValueError``
     some_list.remove(4)
  except (IndexError, ValueError), e:
     print("Caught again!")
     print(e)
  ```
  **Output (Python 2.x):**
  ```
  Caught again!
  list.remove(x): x not in list
  ```
  **Output (Python 3.x):**
  ```py
    File "<input>", line 4
      except (IndexError, ValueError), e:
                                       ^
  IndentationError: unindent does not match any outer indentation level
  ```

* Separating the exception from the variable with a comma is deprecated and does not work in Python 3; the correct way is to use `as`. Example,
  ```py
  some_list = [1, 2, 3]
  try:
      some_list.remove(4)

  except (IndexError, ValueError) as e:
      print("Caught again!")
      print(e)
  ```
  **Output:**
  ```
  Caught again!
  list.remove(x): x not in list
  ```

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
The midnight time is not printed.

#### 💡 Explanation:

Before Python 3.5, the boolean value for `datetime.time` object was considered to be `False` if it represented midnight in UTC. It is error-prone when using the `if obj:` syntax to check if the `obj` is null or some equivalent of "empty."

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


#### 💡 Explanation:

* Booleans are a subclass of `int`
  ```py
  >>> isinstance(True, int)
  True
  >>> isinstance(False, int)
  True
  ```

* The integer value of `True` is `1` and that of `False` is `0`.
  ```py
  >>> True == 1 == 1.0 and False == 0 == 0.0
  True
  ```

* See this StackOverflow [answer](https://stackoverflow.com/a/8169049/4354153) for rationale behind it.

---

### Needle in a Haystack

Almost every Python programmer would have faced this situation.

```py
t = ('one', 'two')
for i in t:
    print(i)

t = ('one')
for i in t:
    print(i)

t = ()
print(t)
```

**Output:**
```py
one
two
o
n
e
tuple()
```


#### 💡 Explanation:
* The correct statement for expected behavior is `t = ('one',)` or `t = 'one',` (missing comma) otherwise the interpreter considers `t` to be a `str` and iterates over it character by character.
* `()` is a special token and denotes empty `tuple`.

---

### The surprising comma

Suggested by @MostAwesomeDude in [this](https://github.com/satwikkansal/wtfPython/issues/1) issue.

**Output:**
```py
>>> def f(x, y,):
...     print(x, y)
...
>>> def g(x=4, y=5,):
...     print(x, y)
...
>>> def h(x, **kwargs,):
  File "<stdin>", line 1
    def h(x, **kwargs,):
                     ^
SyntaxError: invalid syntax
>>> def h(*args,):
  File "<stdin>", line 1
    def h(*args,):
                ^
SyntaxError: invalid syntax
```

#### 💡 Explanation:

- Trailing comma is not always legal in formal parameters list of a Python function.
-  In Python, the argument list is defined partially with leading commas and partially with trailing commas. This conflict causes situations where a comma is trapped in the middle, and no rule accepts it.
-  **Note:** The trailing comma problem is [fixed in Python 3.6](https://bugs.python.org/issue9232). The remarks in [this](https://bugs.python.org/issue9232#msg248399) post discuss in brief different usages of trailing commas in Python.

---

### For what?

Suggested by @MittalAshok in [this](https://github.com/satwikkansal/wtfpython/issues/23) issue.

```py
some_string = "wtf"
some_dict = {}
for i, some_dict[i] in enumerate(some_string):
    pass
```

**Outuput:**
```py
>>> some_dict # An indexed dict is created.
{0: 'w', 1: 't', 2: 'f'}
```

####  💡 Explanation:

* A `for` statement is defined in the [Python grammar](https://docs.python.org/3/reference/grammar.html) as:
  ```
  for_stmt: 'for' exprlist 'in' testlist ':' suite ['else' ':' suite]
  ```
  Where `exprlist` is the assignment target. This means that the equivalent of `{exprlist} = {next_value}` is **executed for each item** in the iterable.
  An interesting example suggested by @tukkek in [this](https://github.com/satwikkansal/wtfPython/issues/11) issue illustrates this:
  ```py
  for i in range(4):
      print(i)
      i = 10
  ```

  **Output:**
  ```
  0
  1
  2
  3
  ```

  Did you expect the loop to run just once?

  **💡 Explanation:**

  - The assignment statement `i = 10` never affects the iterations of the loop because of the way for loops work in Python. Before the beginning of every iteration, the next item provided by the iterator (`range(4)` this case) is unpacked and assigned the target list variables (`i` in this case).

* The `enumerate(some_string)` function yields a new value `i` (A counter going up) and a character from the `some_string` in each iteration. It then sets the (just assigned) `i` key of the dictionary `some_dict` to that character. The unrolling of the loop can be simplified as:
  ```py
  >>> i, some_dict[i] = (0, 'w')
  >>> i, some_dict[i] = (1, 't')
  >>> i, some_dict[i] = (2, 'f')
  >>> some_dict
  ```

---

### not knot!

Suggested by @MostAwesomeDude in [this](https://github.com/satwikkansal/wtfPython/issues/1) issue.

```py
x = True
y = False
```

**Output:**
```py
>>> not x == y
True
>>> x == not y
  File "<input>", line 1
    x == not y
           ^
SyntaxError: invalid syntax
```

#### 💡 Explanation:

* Operator precedence affects how an expression is evaluated, and `==` operator has higher precedence than `not` operator in Python.
* So `not x == y` is equivalent to `not (x == y)` which is equivalent to `not (True == False)` finally evaluating to `True`.
* But `x == not y` raises a `SyntaxError` because it can be thought of being equivalent to `(x == not) y` and not `x == (not y)` which you might have expected at first sight.
* The parser expected the `not` token to be a part of the `not in` operator (because both `==` and `not in` operators have same precedence), but after not being able to find a `in` token following the `not` token, it raises a `SyntaxError`.

---

### Let's see if you can guess this?

Suggested by @PiaFraus in [this](https://github.com/satwikkansal/wtfPython/issues/9) issue.

```py
a, b = a[b] = {}, 5
```

**Output:**
```py
>>> a
{5: ({...}, 5)}
```

#### 💡 Explanation:

* According to [Python language reference](https://docs.python.org/2/reference/simple_stmts.html#assignment-statements), assignment statements have the form
  ```
  (target_list "=")+ (expression_list | yield_expression)
  ```
  and
  > An assignment statement evaluates the expression list (remember that this can be a single expression or a comma-separated list, the latter yielding a tuple) and assigns the single resulting object to each of the target lists, from left to right.

* The `+` in `(target_list "=")+` means there can be **one or more** target lists. In this case, target lists are `a, b` and `a[b]` (note the expression list is exactly one, which in our case is `{}, 5`).

* After the expression list is evaluated, it's value is unpacked to the target lists from **left to right**. So, in our case, first the `{}, 5` tuple is unpacked to `a, b` and we now have `a = {}` and `b = 5`.

* `a` is now assigned to `{}` which is a mutable object.

* The second target list is `a[b]` (you may expect this to throw an error because both `a` and `b` have not been defined in the statements before. But remember, we just assigned `a` to `{}` and `b` to `5`).

* Now, we are setting the key `5` in the dictionary to the tuple `({}, 5)` creating a circular reference (the `{...}` in the output refers to the same object that `a` is already referencing). Another simpler example of circular reference could be
  ```py
  >>> some_list = some_list[0] = [0]
  >>> some_list
  [[...]]
  >>> some_list[0]
  [[...]]
  >>> some_list is some_list[0]
  [[...]]
  ```
  Similar is the case in our example (`a[b][0]` is the same object as `a`)

* So to sum it up, you can break the example down to
  ```py
  a, b = {}, 5
  a[b] = a, b
  ```
  And the circular reference can be justified by the fact that `a[b][0]` is the same object as `a`
  ```py
  >>> a[b][0] is a
  True
  ```

---

###  Minor Ones

* `join()` is a string operation instead of list operation. (sort of counter-intuitive at first usage)
  
  **💡 Explanation:**
  If `join()` is a method on a string then it can operate on any iterable (list, tuple, iterators). If it were a method on a list, it'd have to be implemented separately by every type. Also, it doesn't make much sense to put a string-specific method on a generic `list` object API.

* Few weird looking but semantically correct statements:
  + `[] = ()` is a semantically correct statement (unpacking an empty `tuple` into an empty `list`)
  + `'a'[0][0][0][0][0]` is also a semantically correct statement as strings are [sequences](https://docs.python.org/3/glossary.html#term-sequence)(iterables supporting element access using integer indices) in Python.
  + `3 --0-- 5 == 8` and `--5 == 5` are both semantically correct statements and evaluate to `True`.

* Given that `a` is a number, `++a` and `--a` are both valid Python statements, but don't behave the same way as compared with similar statements in languages like C, C++ or Java.
  ```py
  >>> a = 5
  >>> a
  5
  >>> ++a
  5
  >>> --a
  5
  ```

  **💡 Explanation:**
  + There is no `++` operator in Python grammar. It is actually two `+` operators.
  + `++a` parses as `+(+a)` which translates to `a`. Similarly, the output of the statement `--a` can be justified.
  + This StackOverflow [thread](https://stackoverflow.com/questions/3654830/why-are-there-no-and-operators-in-python) discusses the rationale behind the absence of increment and decrement operators in Python.

* Python uses 2 bytes for local variable storage in functions. In theory, this means that only 65536 variables can be defined in a function. However, python has a handy solution built in that can be used to store more than 2^16 variable names. The following code demonstrates what happens in the stack when more than 65536 local variables are defined (Warning: This code prints around 2^18 lines of text, so be prepared!):
     ```py
     import dis
     exec("""
     def f():*     """ + """
         """.join(["X"+str(x)+"=" + str(x) for x in range(65539)]))

     f()

     print(dis.dis(f))
     ```

* Multiple Python threads won't run your *Python code* concurrently (yes you heard it right!). It may seem intuitive to spawn several threads and let them execute your Python code concurrently, but, because of the [Global Interpreter Lock](https://wiki.python.org/moin/GlobalInterpreterLock) in Python, all you're doing is making your threads execute on the same core turn by turn. Python threads are good for IO-bound tasks, but to achieve actual parallelization in Python for CPU-bound tasks, you might want to use the Python [multiprocessing](https://docs.python.org/2/library/multiprocessing.html) module.

* List slicing with out of the bounds indices throws no errors
  ```py
  >>> some_list = [1, 2, 3, 4, 5]
  >>> some_list[111:]
  []
  ```

---



## Conclusion
Cái này chúng tôi gọi là Python **chất** đến từng dòng code!

## References
- [wtfpython](https://github.com/satwikkansal/wtfpython)