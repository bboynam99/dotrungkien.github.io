Đùa chút thôi, các bạn vẫn hoàn toàn có thể sử dụng thuật toán TF/IDF như cũ, chỉ cần thay đổi config của `similarity` thôi.
<br>
**Note**:
- Trong Elasticsearch có rất nhiều `similarity module` implement thuật toán đánh giá similarity giữa các kết quả, TF/IDF và BM là một trong số đó, các thuật toán khác các bạn có thể tham khảo thêm [tại đây](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-similarity.html)
- Elastic search phiên bản 2.4 trở về trước thì sẽ mặc định similarity là `classic` (tức TF/IDF)
- Elastic search phiên bản 5.0 trở lên thì sẽ mặc định similarity là `BM25`
Vì giới hạn bài viết, mình sẽ không đi sâu quá vào theory của BM25 mà sẽ show công thức luôn. Các bạn có thể tìm hiểu thêm trên google & wiki.

## BM25
### IDF trong BM25
công thức của BM25 IDF là:

```
idf(t) = log(1 + (docCount - docFreq + 0.5) / (docFreq + 0.5))
```

Trong đó:

- `docCount`: số lượng document
- `docFreq`: số lượng document chứa term

### TF trong BM25
trong BM25 thì term frequency được tính theo công thức:

```
((k + 1) * freq) / (k + freq)
```
Trong đó:

- `k`: hằng số (thường là 1.2)
- `freq`: frequency của term trong document

![](http://opensourceconnections.com/blog/uploads/2015/TF1.png)


Như các bạn có thể nhìn thấy, đường BM25 TF score tiệm cận dần đến (k+1), trong trường hợp này là k=1.2 (mặc định). tf càng cao thì relevance càng cao. Với BM25 thì k thường được xét giá trị mặc định là 1.2. Tất nhiên bạn có thể điều chỉnh giá trị này để phù hợp với mô hình. Tuy nhiên chú ý một điều là set k càng lớn thì độ hội tụ sẽ càng lâu.

### Document Length trong BM25
Thực ra công thức TF bên trên kia là chưa thực sự hoàn chỉnh, nó đúng với những document có độ dài trung bình trong toàn bộ index. Nếu độ dài document quá ngắn hoặc quá dài so với độ dài trung bình, thì công thức trên sẽ cho kết quả thiếu chính xác.
<br>
Vì thế người ta thêm vào trong công thức trên 2 tham số, một hằng số b và một giá trị độ dài L, công thức sẽ trở thành:

```
((k + 1) * freq) / (k * (1.0 - b + b * L) + freq)
```

trong đó b=0.75 (mặc định), L là tỉ lệ giữa độ dài của document so với độ dài trung bình của tất cả documents.

```
L = fieldLength / avgFieldLength
```

Cũng như k, bạn có thể điều chỉnh b để phù hợp với mô hình bạn xây dựng. b càng gần 0 thì độ ảnh hưởng của document length càng nhỏ, và ngược lại, b càng lớn thì độ ảnh hưởng của document length càng lớn.
<br>
Đây là biểu đồ thể hiện giá trị của TF đối với các độ dài khác nhau của document:

![](http://opensourceconnections.com/blog/uploads/2015/NORMS1.png)

<br>

### All Together
Ta công thức cuối cùng của BM25
```
IDF * (freq * (k1 + 1)) / (freq + k1 * (1 - b + b * (fieldLength / avgFieldLength)))
```
### Time for action - round 2
Chuẩn bị dữ liệu và test:

```yml
DELETE /my_index
PUT /my_index
{ "settings": { "number_of_shards": 1 }}

POST /my_index/my_type/_bulk
{ "index": { "_id": 1 }}
{ "title": "The quick brown fox" }
{ "index": { "_id": 2 }}
{ "title": "The quick brown fox jumps over the lazy dog" }
{ "index": { "_id": 3 }}
{ "title": "The quick brown fox jumps hahaha over the quick dog" }
{ "index": { "_id": 4 }}
{ "title": "Brown fox hahaha brown dog" }

GET /my_index/my_type/_search
{
  "explain": true,
  "query": {
    "term": {
      "title": "hahaha"
    }
  }
}
```

**Expected:** kết quả cho `_id=3` (`_id=4` các bạn có thể tự làm)
- `idf`: docCount = 4, docFreq = 2
```
  log(1 + (docCount - docFreq + 0.5) / (docFreq + 0.5))
= log(1 + (4-2 + 0.5) / (2+0.5))
= 0.6931471805599453
```
- TF with document Length: freq = 1 (trong doc chỉ có 1 "hahaha"), k = 1.25, b = 0.75, fieldLength = 10, agvLength = 7
```
  (freq * (k1 + 1)) / (freq + k1 * (1 - b + b * fieldLength / avgFieldLength))
= (1 * (1.25 + 1) / (1 + 1.25 * (1 - 0.75 + 0.75 * (10. / 7))))
= 0.8484848484848484
```
Vậy `_score = 0.6931471805599453*0.8484848484848484 = 0.588124`

**Actual:**
```
...
"_score": 0.58279467,
...
"value": 0.6931472,
"description": "idf, computed as log(1 + (docCount - docFreq + 0.5) / (docFreq + 0.5)) from:",
...
"value": 0.840795,
"description": "tfNorm, computed as (freq * (k1 + 1)) / (freq + k1 * (1 - b + b * fieldLength / avgFieldLength)) from:",
```
Kết quả gần như chính xác hoàn, tuy nhiên có sai số là do đâu ? Nhìn vào kết quả actual, ta nhận thấy một điểm lạ, đó là `fieldLength = 10.24` chứ không phải là 10 ?
<br>
Tại sao lại như vậy? Câu trả lời cũng giống như field-Length norm ở bên trên, giá trị được lưu là 8 bit, nên trong quá trình encode với decode dữ liệu đã bị sai khác đi một chút. Tuy nhiên kết quả vẫn có thể chấp nhận được.

## Conclusion
Vậy là chúng ta đã đi qua 2 thuật toán để tính toán `_score` trong Elasticsearch. Hi vọng các bạn có những trải nghiệm thú vị :D
<br>
#### What's next?
Có rất nhiều topic liên quan tới chủ đề này mà mình nghĩ các bạn có thể tìm hiểu thêm như:

 - Boost query
 - analyzed or non_analyzed
 - Vector space model
 - Các vấn đề liên quan đến perfomance của Elasticsearch
 - ...

Enjoy coding !  


- [How scoring works in Elasticsearch](https://www.compose.com/articles/how-scoring-works-in-elasticsearch/)
- [Under the hood of the search engine](https://findwise.com/blog/under-the-hood-of-the-search-engine/)
- [BM25 The Next Generation of Lucene Relevance](http://opensourceconnections.com/blog/2015/10/16/bm25-the-next-generation-of-lucene-relevation/)
