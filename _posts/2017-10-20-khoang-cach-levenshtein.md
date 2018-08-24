---
layout: post
title: "Khoảng cách Levenshtein và fuzzy query trong Elasticsearch"
tags: Levenshtein fuzzy query Elasticsearch dynamic-programming
---
Chào các bạn, quay lại với Elasticsearch, hôm nay chúng ta sẽ đến với một chủ đề khác trong fulltext search: **fuzzy query**.

Khi làm việc với Elasticsearch, hẳn là các bạn không lạ gì với fuzzy query, tuy nhiên nếu không hiểu về cách mà fuzzy query hoạt động, thì rất có thể việc search của bạn sẽ cho ra những kết quả như "trên trời rơi xuống" vậy. Vì vậy hiểu và nắm được nguyên lý của fuzzy query là một điều rất quan trọng trong fulltext search.

## Khoảng cách Levenshtein

Để hiểu được nguyên lý của fuzzy query, trước hết ta phải nắm được về *khoảng cách Levenshtein*: **Khoảng cách Levenshtein** thể hiện khoảng cách khác biệt giữa 2 chuỗi ký tự.

Khoảng cách Levenshtein giữa chuỗi S1 và chuỗi S2 là số bước ít nhất biến chuỗi S1 thành chuỗi S2 thông qua 3 phép biến đổi là:

- xoá 1 ký tự.
- thêm 1 ký tự.
- thay ký tự này bằng ký tự khác.

Ví dụ: Khoảng cách Levenshtein giữa 2 chuỗi "kitten" và "sitting" là 3, vì phải dùng ít nhất 3 lần biến đổi.

1. kitten -> sitten (thay "k" bằng "s")
2. sitten -> sittin (thay "e" bằng "i")
3. sittin -> sitting (thêm ký tự "g")

Rất rõ ràng, tuy nhiên vấn đề là làm thế nào để ta xác định được chuỗi biến đổi **ngắn nhất** đó. Về cơ bản đây là một bài lập trình thuật toán thuần tuý. Chúng ta có thể giải nó bằng **quy hoạch động** như sau:

- Không mất tính tổng quát, giả sử s1 >= s2 (tức chuỗi s1 dài hơn chuỗi s2).
- Ta sẽ xây dựng mảng previous_row để lưu trữ khoảng cách Levenshtein từ mỗi chuỗi tạo bởi i ký tự đầu tiên của s1 với j ký tự đầu tiên của s2.
- Khởi tạo previous_row bằng chuỗi khoảng cách tạo bởi i ký tự đầu tiên của s1 với 0 ký tự đầu của s2, khoảng cách này dĩ nhiên bằng i.
- Tại mỗi vị trí (i, j), ta sẽ có công thức tính khoảng cách như sau: distance = min(insertion, deletions, subtitutions), nghĩa là tại đó phép nào cho khoảng cách ngắn nhất thì lấy.

```py
def levenshtein(s1, s2):
    if len(s1) < len(s2):
        return Levenshtein(s2, s1)
    if len(s2) == 0:
        return len(s1)

    previous_row = range(len(s2) + 1)
    for i, c1 in enumerate(s1):
        current_row = [i + 1]
        for j, c2 in enumerate(s2):
            insertions = previous_row[j + 1] + 1
            deletions = current_row[j] + 1
            substitutions = previous_row[j] + (c1 != c2)
            current_row.append(min(insertions, deletions, substitutions))
        previous_row = current_row

    return previous_row[-1]
print levenshtein("kitten", "sitting")
```

## Khoảng cách Levenshtein với Non-Ascii string

Trong hầu hết các ví dụ của lập trình, ta thông thường đều chỉ làm việc với tiếng Anh, kết quả thường đẹp tuyệt vời. Nhưng đời không như mơ, ngoài tiếng Anh, chúng ta còn thường xuyên gặp phải tiếng Việt, tiếng Nhật nữa, và liệu rằng những gì chúng ta tính toán trên kia có còn đúng nữa không ? chúng ta sẽ kiểm tra.

```py
>>> print levenshtein("con đường", "cân đường")
2
```

(dafuq) không phải 1 sao ?

```py
>>> print levenshtein("たいへん", "たいひひ")
3
```

(dafuq) không phải 2 sao ?
Thật không thể tin nổi ? sao kết quả lại không như mong đợi ?

Nguyên nhân của điều này là do **multibyte character**. Với các kí tự trong các ngôn ngữ khác tiếng Anh, chúng ta đôi khi phải dùng nhiều hơn 1 byte để biểu diễn. Ví dụ như trên là ký tự "â, đ, ư, ờ" và các kí tự tiếng Nhật. Ta check thử từng kí tự:

```py
>>> for i in "con đường": print ord(i),
>>> for i in "cân đường": print ord(i),
99 111 110 32 196 145 198 176 225 187 157 110 103
99 195 162 110 32 196 145 198 176 225 187 157 110 103
```

```py
>>> for i in "たいへん": print ord(i),
>>> for i in "たいひひ": print ord(i),
227 129 159 227 129 132 227 129 184 227 130 147
227 129 159 227 129 132 227 129 178 227 129 178
```

Ra vậy, chúng đã lộ nguyên hình là các multibyte character, nhẩm tính thử, ta thấy các kết quả khoảng cách ta có như trên là **hoàn toàn chính xác**.

### Fuzzy query trong Elasticsearch

Fuzzy query trong Elasticsearch cũng sử dụng khoảng cách Levenshtein, và cho phép ta config tham số *fuzziness* để cho kết quả phù hợp nhất với nhu cầu của mình:

- 0, 1, 2: Là khoảng cách Levenshtein lớn nhất được chấp thuận. Nghĩa là trong ví dụ trên nếu bạn đặt fuzziness=3 thì "cân đường" sẽ không được tìm thấy với từ khoá "con đường"
- AUTO: Sẽ tự động điều chỉnh kết quả dựa trên độ dài của term. Cụ thể:
    - 0..2: bắt buộc match chính xác (khoảng cách Levenshtein lớn nhất là 0)
    - 3..5: khoảng cách Levenshtein lớn nhất là 1
    - 5 trở lên: khoảng cách Levenshtein lớn nhất là 2

Nếu trang web bạn đang sử dụng không phải là tiếng Anh, thì khi tìm kiếm fulltext search, có thể bạn sẽ phải chú ý điều chỉnh config giá trị fuzzy query hợp lý để đưa ra kết quả gần nhất với mong đợi. Nhiều khi AUTO chưa chắc đã là tốt.

## Kết luận

Fulltext search rất linh hoạt, nhưng chính sự linh hoạt đó đôi khi khiến lập trình viên trở nên bối rối trong quá nhiều sự lựa chọn.

Enjoy coding !

## Tham khảo

- [Levenshtein Distance](https://en.wikipedia.org/wiki/Levenshtein_distance)
- [Fuzzy query](https://www.elastic.co/guide/en/Elasticsearch/reference/current/common-options.html#fuzziness)