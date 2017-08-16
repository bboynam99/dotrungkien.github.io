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
Mục đích chính để dùng Elasicsearch là gì ? - **Tìm kiếm**, dĩ nhiên là ES có thể làm được nhiều điều hơn thế, nhưng cái quan trọng nhất vẫn là tìm kiếm. Và theo mình thì khi dùng bất cứ công cụ gì, ngoài việc biết cách sử dụng nó, thì việc tìm hiểu xem nó hoạt động ra sao cũng quan trọng và thú vị không hề kém.
<br>
Khi tìm kiếm, Elascticsearch (ES) trả về cho mình ngoài các kết quả tìm được, còn có đánh giá `relevance` (độ liên quan) của kết quả dựa trên giá trị thực dương *_score*. ES sẽ sắp xếp các kết quả trả về của các query theo thứ tự *_score* giảm dần. Đây là điểm mà mình thấy rất thú vị trong ES, và mình sẽ dành bài viết này để nói về cách làm thế nào người ta tính toán và đưa ra được giá trị *_score* đó.
<br>
**Note**:

- Elasticsearch là tên của search engine, không phải là tên thuật toán!
- Một số thuật ngữ rất hay được sử dụng trong Elasticsearch, mình sẽ giữ nguyên để giữ gìn sự trong sáng của thuật ngữ, đồng thời cũng tiện cho việc tra cứu. Các thuật ngữ mình nhắc tới ở đây là: **relevance**, **index** (tương đương với database trong mysql), **type** (tương đương với table trong mysql), **document** (tương ứng với record trong mysql), **term** (từ, từ khóa), **field** (có thể gọi là *trường* - nhưng mình không thích lắm vì nó có cấu trúc, nên quyết định giữ nguyên)

## TF/IDF

Thuật toán đánh giá được sử dụng rất phổ biến trong Elasticsearch có tên gọi là *term frequency/inverse document frequency*, gọi tắt là *TF/IDF*, bao gồm các yếu tố đánh giá: [Term frequency](#term-frequency), [Inverse document frequency](#inverse-document-frequency), [Field-length norm](#field-length-norm).

### Term frequency
Yếu tố này đánh giá tần suất xuất hiện của term trong field. Càng xuất hiện nhiều, relevance càng cao. Dĩ nhiên rồi, một field mà từ khoá xuất hiện 5 lần sẽ cho relevance cao hơn là field mà từ khoá chỉ xuất hiện 1 lần.
<br>
Ví dụ: nếu bạn search với từ khoá "quick", thì rõ ràng field bên trên sẽ cho TF cao hơn (xuất hiện 2 lần) filed bên dưới (xuất hiện 1 lần):

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
Ví dụ thế này, bạn muốn tìm kiếm thông tin về Framgia. Khi bạn search google với từ khoá "công ty", thì nhận được khoảng 170,000,000 kết quả, nhưng với 170,000,000 kết quả đó, rất khó để biết được kết quả nào là kết quả chúng ta thực sự muốn tìm, suy ra rằng từ khoá "công ty" sẽ có giá trị rất thấp. Tuy nhiên, nếu bạn search với từ khoá "công ty Framgia", số lượng kết quả thu gọn lại chỉ còn khoảng 3,000,000 kết quả, và bạn thấy rõ ràng rằng các kết quả này rất sát với kết quả bạn mong muốn. Vì thế từ khoá ít xuất hiện hơn ("công ty Framgia") sẽ cho relevance cao hơn là từ khoá xuất hiện nhiều hơn("công ty") trên `toàn bộ index`.
<br>
Mặc dù vậy, kết quả trên không có nghĩa là từ khoá "công ty Framgia" tốt hơn từ khoá "công ty" gần 60 lần. TDF được đánh giá theo công thức sau:

$$idf(t) = 1 + \log \frac{numDocs}{docFreq + 1} $$

*Giải thích*: inverse document frequency (idf) của t là logarit cơ số e (logarit tự nhiên) của thương giữa tổng số documents trong index và số documents xuất hiện t (giá trị công thêm 1 ở đây để tránh xảy ra lỗi [Division by zero](https://en.wikipedia.org/wiki/Division_by_zero)).

theo đó thì kết quả đã được lấy logarit đi nên con số 60 bên trên đã giảm kha khá rồi.

### Field-length norm
Yếu tố này đánh giá độ dài của field. Field càng ngắn, thì term sẽ có weight càng cao; và ngược lại. Điều này hoàn toàn dễ hiểu, bạn có thể thấy một từ xuất hiện trong *title* sẽ có giá trị hơn rất nhiều cũng từ đó nhưng xuất hiện trong *content*.

Công thức:

$$norm(d) = \frac{1}{\sqrt{numTerms}}$$

*Giải thích*: field-length norm (norm) là nghịch đảo của căn bậc hai số lượng term trong field. (có thể hiểu là số lượng chữ của field đó)

### Putting it together
*_score* cuối cùng sẽ là tích của 3 giá trị trên:

```
IDF score * TF score * fieldNorms
```

hay

```
log(numDocs / (docFreq + 1)) * sqrt(tf) * (1/sqrt(length))
```
<br>
**Note**:

- `numDocs` chính là `maxDocs`, đôi khi bao gồm cả những document đã bị delete
- fieldNorm được tính toán và lưu trữ dưới dạng 8 bit floating point number. Nên khi tính toán, hệ thống sẽ encode và decode giá trị của fieldNorm về  8 bit, và theo đó, bạn sẽ thấy giá trị filedNorm đôi khi hơi khác so với tính toán một chút xíu xíu thôi. Nếu muốn hiểu thêm bạn có thể tham khảo [tại đây](https://lucene.apache.org/core/5_1_0/core/org/apache/lucene/search/similarities/DefaultSimilarity.html#encodeNormValue(float))

> Expert: Default scoring implementation which encodes norm values as a single byte before being stored. At search time, the norm byte value is read from the index directory and decoded back to a float norm value. This encoding/decoding, while reducing index size, comes with the price of precision loss - it is not guaranteed that decode(encode(x)) = x. For instance, decode(encode(0.89)) = 0.875.


### Time for action
Ta tạo một dữ liệu như sau:

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
Expected kết quả:

- `tf`: có 1 kết quả/1 doc, vậy `tf = 1.0`
- `idf`: tính toán theo công thức `1 + log((numDocs)/(docFreqs+1)) = 1+log(1/2) = 0.30685282`
- `fieldNorm`: field "quick brown fox" có độ dài 3, vậy `filedNorm = 1/sqrt(3) = 0.577`

Actual:

```yaml
weight(text:fox in 0) [PerFieldSimilarity]:  0.15342641
result of:
    fieldWeight in 0                         0.15342641
    product of:
        tf(freq=1.0), with freq of 1:        1.0
        idf(docFreq=1, maxDocs=1):           0.30685282
        fieldNorm(doc=0):                    0.5
```
Yeah, kết quả giống với mong đợi (về fieldNorm thì đã có giải thích phía bên trên).

### Everything is not so simple
Cảm ơn bạn đã đọc đến đây, nhưng trên thực tế, kể từ bản 5.0, TF/IDF đã không còn được sử dụng như thuật toán default cho `similarity module` trong Elasticsearch nữa rồi. Nghĩa là bạn không cần đọc từ đầu tới giờ cũng không sao!

![](http://i0.kym-cdn.com/photos/images/original/000/407/951/159.jpg)

Mình nhận ra được điều này khi chạy thực tế đoạn code mình viết bên trên kia (yaoming again =))). Các hệ thống sử dụng Elasticsearch phiên bản 2.4 trở về trước thì TF/IDF sẽ là thuật toán mặc định. Tuy nhiên các bạn không cần phải lo lắng gì cả, vì đơn giản là ta chỉ cần thay đổi một chút config là có thể thay đổi qua lại giữa các `similarity module` rồi. Kể từ bản 5.0 thì module `similarity module` mặc định của ES sẽ là `BM25`, còn TF/IDF sẽ là `classic`. Ngoài ra cũng có một số thuật toán khác, xem thêm [tại đây](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-similarity.html)

## BM25 - Best Match 25
Thực chất, BM25 vẫn dựa trên nền tảng của TF/IDF, và cải tiến dựa trên lý thuyết [probabilitistic information retrieval](https://nlp.stanford.edu/IR-book/html/htmledition/probabilistic-information-retrieval-1.html)

Do BM25 vẫn dựa trên nền tảng của TF/IDF, nên nó sẽ vẫn có các yếu tố TF và IDF

### IDF
Điểm khác biệt lớn nhất giữa classic IDF và BM52 IDF chính là BM52 IDF có khả năng đưa ra giá trị âm cho term mà có tần suất xuất hiện rất lớn trong document. Theo đó, trong công thức tính toán, trước khi tính log, ta sẽ cộng thêm 1 vào giá trị, nên nó khoogn thể tính toán với số âm.

```
IDF = log(1 + (docCount - docFreq + 0.5) / (docFreq + 0.5))
```

Đây là đồ thị so sánh giữa classic IDF và BM25 IDF:

![](http://opensourceconnections.com/blog/uploads/2015/IDF1.png)

Enjoy coding!

## Tham khảo
- [Inverse Document Frequency and the Importance of Uniqueness
](!https://moz.com/blog/inverse-document-frequency-and-the-importance-of-uniqueness)
- [What is relevance?](https://www.elastic.co/guide/en/elasticsearch/guide/current/relevance-intro.html#relevance-intro)
- [Theory behind relevance scoring](https://www.elastic.co/guide/en/elasticsearch/guide/current/scoring-theory.html#scoring-theory)
