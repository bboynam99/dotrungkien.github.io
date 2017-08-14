---
layout: post
comments: true
title:  "Thuật toán đánh giá _score trong Elasticsearch"
date:   2017-08-14 9:00:00
permalink: 2017/08/14/thuat-toan-danh-gia-score-trong-elasticsearch/
mathjax: true
tags: Elasticsearch algorithm TF-IDF
---

**Elasticsearch** là một engine search đã quá nổi tiếng rồi!
<br>
Mục đích chính để dùng Elasicsearch là gì ? - **Tìm kiếm**, dĩ nhiên là ES có thể làm được nhiều điều hơn thế, nhưng cái quan trọng nhất vẫn là tìm kiếm. Và theo mình thì khi dùng bất cứ công cụ gì, ngoài việc biết cách sử dụng nó, thì việc tìm hiểu xem nó hoạt động ra sao cũng thú vị và quan trọng không hề kém.
<br>
Khi tìm kiếm, Elascticsearch (ES) trả về cho mình ngoài các kết quả tìm được, còn có đánh giá "độ thích hợp" của kết quả dựa trên giá trị *_score*. ES sẽ sắp xếp các kết quả trả về của các query theo thứ tự *_score* giảm dần. Đây là điểm mà mình thấy rất thú vị trong ES, và mình sẽ dành bài viết này để nói về cách làm thế nào người ta tính toán và đưa ra được giá trị *_score* đó.
<br>
**Datatype**: *_score* là một số thực dương nằm trong khoảng [0,1]
<br>
**Note**:

- Elasticsearch là tên của search engine, không phải là tên thuật toán!
- Một số thuật ngữ rất hay được sử dụng trong Elasticsearch, mình sẽ giữ nguyên để giữ gìn sự trong sáng của thuật ngữ, đồng thời cũng tiện cho việc tra cứu. Các thuật ngữ mình nhắc tới ở đây là: **index** (tương đương với database trong mysql), **type** (tương đương với table trong mysql), **document** (tương ứng với record trong mysql), **term** (từ, cụm từ), **field** (có thể gọi là *trường* - nhưng mình không thích lắm vì nó có cấu trúc, nên quyết định giữ nguyên)

Nhắc lại một chút, đơn vị lưu trữ của ES là `document`, ứng với mỗi loại query mà *_score* sẽ được tính khác nhau cho mỗi document:

- với `fuzzy` query thì *_score* được tính toán bởi độ giống nhau về **spelling** giữa *term* được query và kết quả.
- với `terms` query thì kết quả sẽ là tần suất xuất hiện của term

Tuy nhiên thông thường, "độ thích hợp" được sử dụng chung cho việc đánh giá độ gần gũi (similarity) một full-text field với full-text trong query.
<br>
Thuật toán đánh giá được sử dụng trog Elasticsearch có tên gọi là *term frequency/inverse document frequency*, gọi tắt là *TF/IDF*, bao gồm các yếu tố đánh giá: [Term frequency](#term-frequency), [Inverse document frequency](#inverse-document-frequency), [Field-length norm](#field-length-norm).

### Term frequency
Yếu tố này đánh giá tần suất xuất hiện của term trong `field`. Càng xuất hiện nhiều, độ thích hợp càng cao. Dĩ nhiên rồi, một field mà từ khoá xuất hiện 5 lần sẽ cho độ thích hợp cao hơn là field mà từ khoá chỉ xuất hiện 1 lần.
<br>
Ví dụ: nếu bạn search với từ khoá "quick", thì rõ ràng field bên trên sẽ cho TF tốt hơn (xuất hiện 2 lần) filed bên dưới (xuất hiện 1 lần):

```yaml
{ "title": "The quick brown fox jumps over the quick dog" }
```

```yaml
{ "title": "The quick brown fox" }
```

TF được tính theo công thức sau:

$$tf(t, d) = \sqrt{frequency}$$

*Giải thích*: term frequency (tf) của t trong document d được tính bằng căn bậc hai của số lần t xuất hiện trong d.

### Inverse document frequency
Yếu tố này đánh giá tần suất xuất hiện của term trên `toàn bộ index`. Điều đáng chú ý ở đây là càng xuất hiện nhiều, càng `ít` thích hợp!

![](http://i0.kym-cdn.com/entries/icons/mobile/000/008/491/dafuq.jpg)


Tại sao lại như vậy ? nghe chừng hơi khó hiểu nhưng thực sự nó rất hợp lý.
<br>
Ví dụ thế này, bạn muốn tìm kiếm thông tin về Framgia. Khi bạn search google với từ khoá "công ty", thì nhận được khoảng 170,000,000 kết quả, nhưng với 170,000,000 kết quả đó, rất khó để biết được kết quả nào là kết quả chúng ta thực sự muốn tìm, suy ra rằng từ khoá "công ty" sẽ có giá trị rất thấp. Tuy nhiên, nếu bạn search với từ khoá "công ty Framgia", số lượng kết quả thu gọn lại chỉ còn khoảng 3,000,000 kết quả, và bạn thấy rõ ràng rằng các kết quả này rất sát với kết quả bạn mong muốn. Vì thế từ khoá ít xuất hiện hơn ("công ty Framgia") sẽ có độ thích hợp cao hơn là từ khoá xuất hiện nhiều hơn("công ty") trên `toàn bộ index`.
<br>
Mặc dù vậy, kết quả trên không có nghĩa là từ khoá "công ty Framgia" tốt hơn từ khoá "công ty" gần 60 lần. TDF được đánh giá theo công thức sau:

$$idf(t) = 1 + \log \frac{numDoc}{docFreq + 1} $$

*Giải thích*: inverse document frequency (idf) của t là logarit của thương giữa tổng số documents trong index và số documents xuất hiện t (giá trị công thêm 1 ở đây để tránh xảy ra lỗi [Division by zero](https://en.wikipedia.org/wiki/Division_by_zero)).

theo đó thì kết quả đã được lấy logarit cơ số 10 đi nên con số 60 bên trên đã giảm kha khá rồi.

### Field-length norm
Yếu tố này đánh giá độ dài của field. Field càng ngắn, thì term sẽ có weight càng cao; và ngược lại. Điều này hoàn toàn dễ hiểu, bạn có thể thấy một từ xuất hiện trong *title* sẽ có giá trị hơn rất nhiều từ đó xuất hiện trong *content*.

Công thức:

$$norm(d) = \frac{1}{\sqrt{numTerms}}$$

*Giải thích*: field-length norm (norm) là nghịch đảo của căn bậc hai số lượng terms trong field.

### Putting it together
3 yếu tố [Term frequency](#term-frequency), [Inverse document frequency](#inverse-document-frequency), [Field-length norm](#field-length-norm) đều được tính toán và lưu trữ trong quá trình đánh index.
<br>
*_score* cuối cùng sẽ là tích của 3 giá trị trên.
<br>
Để test thử, ta chỉ đơn thuần set tham số `explain` bằng true, và ta sẽ thấy được từng điểm số của từng yếu tố.

```
PUT /my_index/doc/1
{ "text" : "quick brown fox" }

GET /my_index/doc/_search?explain
{
  "query": {
    "term": {
      "text": "fox"
    }
  }
}
```
Kết quả:

```yaml
weight(text:fox in 0) [PerFieldSimilarity]:  0.15342641
result of:
    fieldWeight in 0                         0.15342641
    product of:
        tf(freq=1.0), with freq of 1:        1.0
        idf(docFreq=1, maxDocs=1):           0.30685282
        fieldNorm(doc=0):                    0.5
```

Enjoy coding!

## Tham khảo
- [Inverse Document Frequency and the Importance of Uniqueness
](!https://moz.com/blog/inverse-document-frequency-and-the-importance-of-uniqueness)
- [What is relevance?](https://www.elastic.co/guide/en/elasticsearch/guide/current/relevance-intro.html#relevance-intro)
- [Theory behind relevance scoring](https://www.elastic.co/guide/en/elasticsearch/guide/current/scoring-theory.html#scoring-theory)
