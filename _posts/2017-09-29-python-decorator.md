---
layout: post
title: "Python Decorator là gì?"
---
Chào các bạn, hôm nay chúng ta sẽ đến với chủ đề: **Python Decorator**.

Nếu bạn đã biết đến **Decorator Pattern** hay **Java Annotation** thì có lẽ bạn sẽ thấy nó rất quen thuộc và gần gũi. Còn nếu bạn chưa biết, cũng không sao, chúng ta sẽ bắt đầu đi tìm hiểu.

Decorator xuất phát từ mẫu design pattern có tên là **Decorator**, mẫu thiết kế này lấy cảm hứng từ nguyên lý "Open for Extension and Closed for Modification" trong lập trình hướng đối tượng. Decorator có nhiệm vụ đúng như cái tên của nó: "trang trí" - nó trang trí thêm cho các function những thứ function chưa có mà không hề động chạm trực tiếp đến code bên trong.

## About Python Function
Trước khi đến với decorator, chúng ta sẽ ôn lại vài thứ đơn giản về function trong Python trước.

Thực chất mọi thứ trong Python đều là object, cũng giống như Ruby vậy, vì thế nên ta có thể dùng mọi thứ variable, function, class,.. như nhau hết.

Ta có thể dùng function như một biến: 
```python
def greet(name):
    return "hello "+name

greet_someone = greet
print greet_someone("John")

# Outputs: hello John
```

Định nghĩa một function trong một function khác:
```python
def greet(name):
    def get_message():
        return "Hello "

    result = get_message()+name
    return result

print greet("John")

# Outputs: Hello John
```

Function có thể dùng làm tham số cho function khác:
```python
def greet(name):
   return "Hello " + name 

def call_func(func):
    other_name = "John"
    return func(other_name)  

print call_func(greet)

# Outputs: Hello John
```

Giá trị trả về của một function có thể là một function
```python
def compose_greet_func():
    def get_message():
        return "Hello there!"

    return get_message

greet = compose_greet_func()
print greet()

# Outputs: Hello there!
```

OK đủ rồi, đến với decorator thôi.

## Decorator
Ta có ví dụ thế này:
```python
def get_text(name):
   return "lorem ipsum, {0} dolor sit amet".format(name)

def p_decorate(func):
   def func_wrapper(name):
       return "<p>{0}</p>".format(func(name))
   return func_wrapper

my_get_text = p_decorate(get_text)

print my_get_text("John")

# <p>Outputs lorem ipsum, John dolor sit amet</p>
```
Tại đây thì function **p_decorate** dùng tham số đầu vào là một hàm **func** - chính là hàm `get_text`, sau đó "trang trí" thêm cho hàm đó hai tag `<p>` và `</p>` vào, và trả về kết quả là hàm trang trí đó. Đây chính là tư tưởng của decorator trong Python.

Trong Python, cú pháp sử dụng decorator là `@`. Ta viết gọn lại đoạn bên trên sử dụng decorator syntax như sau:
```python
def p_decorate(func):
    def func_wrapper(name):
        return "<p>{0}</p>".format(func(name))
    return func_wrapper

@p_decorate
def get_text(name):
    return "lorem ipsum, {0} dolor sit amet".format(name)
```
và test thử
```python
print get_text("John")

# Outputs <p>lorem ipsum, John dolor sit amet</p>
```

Ta hoàn toàn có thể dùng nhiều decorator cho một function 
```python
def p_decorate(func):
   def func_wrapper(name):
       return "<p>{0}</p>".format(func(name))
   return func_wrapper

def strong_decorate(func):
    def func_wrapper(name):
        return "<strong>{0}</strong>".format(func(name))
    return func_wrapper

def div_decorate(func):
    def func_wrapper(name):
        return "<div>{0}</div>".format(func(name))
    return func_wrapper

@div_decorate
@p_decorate
@strong_decorate
def get_text(name):
   return "lorem ipsum, {0} dolor sit amet".format(name)

print get_text("John")

# Outputs <div><p><strong>lorem ipsum, John dolor sit amet</strong></p></div>
```

## Decorator với function có nhiều tham số
Hàm get_text phía trên chỉ có một tham số là *name*, tuy nhiên trên thực tế các function hoặc có thể không có tham số, hoặc có thể có ít tham số, hoặc có thể có nhiều tham số, túm lại là tham số trong function có thể xuất hiện rất đa dạng.

Và Python cung cấp một cách viết chung nhất cho chúng, là `*args, **kwargs`. Trong đó `*arg` là list các tham số không tên, còn `**kwargs**` là keyword args, nghĩa là các tham số có tên, các bạn có thể xem ví dụ dưới đây để hiểu rõ hơn:
```python
def print_everything(*args):
    for count, thing in enumerate(args):
        print( '{0}. {1}'.format(count, thing))

>>> print_everything('apple', 'banana', 'cabbage')
0. apple
1. banana
2. cabbage
```

```python
def table_things(**kwargs):
    for name, value in kwargs.items():
        print( '{0} = {1}'.format(name, value))

>>> table_things(apple = 'fruit', cabbage = 'vegetable')
cabbage = vegetable
apple = fruit
```

Ta sẽ viết lại decorator với function có tham số như sau
```python
def p_decorate(func):
    def func_wrapper(*args, **kwargs):
        return "<p>{0}</p>".format(func(*args, **kwargs))
    return func_wrapper

@p_decorate
def get_text(name):
    return "lorem ipsum, {0} dolor sit amet".format(name)

print get_text("John")

# Outputs <p>lorem ipsum, John dolor sit amet</p>
```
Theo đó decorator không cần quan tâm là func có bao nhiêu tham số và tham số như thế nào nữa.

### Tham số cho decorator
Ở trên ta đã bàn về tham số trong `func`, thế nhưng ta có thể truyền tham số cho bản thân decorator được không ? Câu trả lời là: được.

Ta có thể truyền tham số cho decorator như dưới đây, bằng cách viết một hàm bên trên decorator, truyền tham số, và trả về decorator đó:
```python
def tags(tag_name):
    def tags_decorator(func):
        def func_wrapper(*args, **kwargs):
            return "<{0}>{1}</{0}>".format(tag_name, func(*args, **kwargs))
        return func_wrapper
    return tags_decorator

@tags("p")
def get_text(name):
    return "Hello "+name

print get_text("John")

# Outputs <p>Hello John</p>
```
Điều này có ích lợi gì? Nó giúp cho ta tránh khỏi việc phải viết nhiều decorator có một kiểu "trang trí" giống nhau. Ta chỉ cần thay đổi vật liệu thôi, còn cách trang trí thì giữ nguyên. Sound good?

## Problems 
Việc viết decorator thật là tiện lợi khi ta có thể linh hoạt thêm vào các thao tác với function mà không cần sửa trực tiếp function đó. 

Nhưng có điều gì bất lợi không? Có, ta thử in ra tên của hàm **get_text**
```python
>>> print get_text.__name__
func_wrapper
```
oops, nó đã trở thành func_wrapper chứ không còn là get_text nữa, bởi vì bản thân các attributes `__name__, __doc__, __module__` của get_text đã bị override bởi func_wrapper rồi.

**Solution**

Thật may, Python cung cấp cho ta một cách để update các attributes đó mà không phải viết lại, đó là decorator `wraps` trong bộ thư viện chuẩn `functools`.

Và mọi thứ trở lại nguyên vẹn như sau:
```python
from functools import wraps

def tags(tag_name):
    def tags_decorator(func):
        @wraps(func)
        def func_wrapper(name):
            return "<{0}>{1}</{0}>".format(tag_name, func(name))
        return func_wrapper
    return tags_decorator

@tags("p")
def get_text(name):
    """returns some text"""
    return "Hello "+name

print get_text.__name__ # get_text
print get_text.__doc__ # returns some text
print get_text.__module__ # __main__
```

## Python built-in Decorators
Có thể bạn đã biết, bản thân Python cũng có 2 buit-in decorator:
- @staticmethod: method với decorator này trong class sẽ biến thành thành static method, nó sẽ không dùng có tham số *self* khi khai báo
- @classmethod: method với decorator này sẽ nhận tham số đầu vào thứ nhất là một class object chứ không phải là một instance của class đó. Nó thường được dùng để tạo ra singleton.

Bạn có thể tham khảo thêm [tại đây](https://stackoverflow.com/questions/136097/what-is-the-difference-between-staticmethod-and-classmethod-in-python)

## Conclusion
Python rất hay, decorator cũng vậy.

Enjoy coding!

## References 
- [A guide to Python's function decorators](https://www.thecodeship.com/patterns/guide-to-python-function-decorators/)
- [PythonDecorators](https://wiki.python.org/moin/PythonDecorators)
- [Python syntax and semantics](https://en.wikipedia.org/wiki/Python_syntax_and_semantics#Decorators)