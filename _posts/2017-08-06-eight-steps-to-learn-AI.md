---
layout: post
title:  "8 bước tiếp cận với Machine Learning"
mathjax: true
tags: AI machine-learning
---
Đâu là nguồn tốt nhất để học AI?

Đây là một câu hỏi rất thường gặp trên Quora, trang web để học và chia sẻ kiến thức rất nổi tiếng.

Và đây là câu trả lời từ `Ben Hamner`, Co-founder đồng thời là CTO của Kaggle:

Bạn thật là may mắn - giờ đây chính là thời điểm tốt hơn bao giờ hết để bắt đầu học về AI, một lĩnh vực đang phát triển với tốc độ chóng mặt trong những năm gần đây. Những chuyên gia đã đưa ra rất nhiều những tool open source với chất lưọng cao; đồng thời với đó là các courses học AI hay blog trên mạng cũng được cập nhật hàng ngày. AI đã khai mở thị trưòng đáng giá hàng tỉ Đô, và tạo ra nhiều những cơ hội việc làm hoàn toàn mới.

Điều đó cũng có nghĩa là bạn sẽ có khả năng bị overload thông tin khi bắt đầu đặt chân vào lĩnh vực này. Và đây là cách tôi tiếp cận. Nếu bạn cảm thấy bị tắc tại bất cứ bước nào, lên Kaggle hoặc các forums và tìm kiếm các câu trả lời là một cách đi rất tốt.


### 1. Hãy tìm một problem mà bạn yêu thích
Bắt đầu với problem mà bạn muốn giải quyết bao giờ cũng dễ chịu hơn nhiều so với việc bạn ngồi tại chỗ và học, học nữa, học mãi (bạn có thể tìm được rất nhiều các problem trên Google nên tôi sẽ không nói chi tiết tại đây). Giải quyết một vấn đề cũng đồng thời thúc đẩy bạn phải tiếp cận machine learning một cách chủ động thay vì thụ động đọc về nó.

Những problem được xem là **tốt** để bắt đầu thường có các tiêu chí sau:
- Nó thuộc phạm trù mà bạn hứng thú
- Bạn có thể thu thập được các dữ liệu phù hợp và sẵn có với problem đó
- Bạn có thể làm việc được với các dữ liệu đó trên một máy tính cá nhân.

Vẫn chưa thấy có gì gần gũi ? Không sao, chúng tôi có cung cấp một ví dụ về ML rất nhỏ, hoàn toàn có thể chạy trên máy bạn, và có thể dùng nó để tham gia tranh tài. Đó là [titanic competition](!https://www.kaggle.com/c/titanic)

### 2. Xây dựng một solution để gỉai quyết vấn đề một cách nhanh nhất
Sẽ rất sai lầm khi bạn mới tiếp cận với Machine Learning mà bạn lại lún sâu vào việc implement sao cho đẹp và tối ưu, tin tôi đi, đầu tiên bạn chỉ cần chạy được, và chạy đúng đã. Mọi thứ khác có hay không, không quan trọng!

Chỉ đơn gỉan là đọc dữ liệu, xử lý dữ liệu, chạy một model cơ bản, xuất kết quả và đánh giá độ chính xác, vậy thôi.

### 3. Cải tiến solution trước đó
Khi này mới là bước bạn tiến hành cải tiến solution tại bưóc 2.
Hãy thử cải thiện từng step, sau đó đánh giá xem nó có ảnh hưỏng như thế nào tới toàn bộ solution của bạn. Và từ đó xem nó có đáng giá để bỏ thời gian ra cải thiện step đó hay không.

Thông thường thì dành thời gian để thu thập nhiều dữ liệu và xử lý đầu vào chính xác sẽ cho kết quả tốt hơn là đi sâu vào tối ưu thuật toán của model. Bởi vì khi bạn có thêm nhiều dữ liệu và hiển thị được nó ra, bạn hoàn toàn có thể phần nào đó hiểu được cấu trúc cũng như sự khác biệt giữa các dữ liệu.

### 4. Viết lại và chia sẻ solution của mình
Cách tốt nhất để nhận được feedback về solution của mình chính là viết lại nó và share trên mạng. Viết lại solution chính là một phương pháp để bạn hiểu được solution một cách rõ ràng và có hệ thống hơn. Đồng thời có một lợi ích rất lớn nữa, đó là nó làm đẹp cho cv của bạn, nó trở thành những showcase để bạn có thể đem đi khoe hoặc đi phỏng vấn.

[Kaggle Datasets](!https://www.kaggle.com/datasets) và [Kaggle Kernels](!https://www.kaggle.com/kernels) là những cách vô cùng hiệu quả để share kết quả và data của bạn, nhận feedback từ những người khác, và theo dõi cách mà họ mở rộng vấn đề của bạn. Nó cũng là cách để bạn nâng cao được profile `Kaggle` của mình.

### 5. Lặp lại các bước 1-4 với hàng loạt các problems khác nhau
Bạn đã hoàn đã hoàn thành được problem đầu tiên mà bạn yêu thích rồi, giờ là lúc bạn sẽ áp dụng những kiến thức đã học được trên những bài toán khác với những dữ liệu khác.
Ví dụ bạn đã làm với các dữ liệu kiểu dạng bảng như csv thông thường, hãy thử với các loại dữ liệu có cấu trúc mới, hoặc dữ liệu hình ảnh chẳng hạn.

Bạn đã gặp nhiều vấn đề khi bạn giải quyết vấn đề về Machine Learning lần đầu tiên? Hẳn là vậy, hầu hết những thành quả sáng tạo và có giá trị đều phải đi từ những thất bại thì mới đến được với những kết quả hoàn thiện. Hãy chọn một bài toán và đương đầu với những khó khăn đó.

[Kaggle Datasets](!https://www.kaggle.com/datasets) và [Kaggle Kernels](!https://www.kaggle.com/kernels) cung cấp những bước khởi đầu rất tốt cho những bài toán đã được thiết kế chuẩn mực và khối lượng data rất phù hợp để với mỗi bài toán. Hãy tự trải nghiệm nhé.

### 6. Nghiêm túc tham gia một contest trên Kaggle nếu bạn chưa từng tham gia
Nỗ lực hết mình để đưa ra kết quả tốt nhất trên cùng một problem mà hàng ngàn người khác cũng đang cùng tham gia giải quyết là một cơ hội học tập rất rất quý báu: nó khiến bạn luôn luôn phải cố gắng để hoàn thiện chương trình và đưa ra được kết quả tốt nhất cho bài toán.

Forum nơi những cá nhân đơn lẻ tranh tài được cung cấp những nguồn tài nguyên dồi dào để người chơi có thể thiết kế solution của mình, cũng như là debug những vấn đề gặp phải. Bên cạnh đó, kernels cung cấp một nền tảng để chạy dễ dàng và nhanh chóng. Và cuối cùng, thì người chiến thắng sẽ được vinh danh với best solution.

Ngoài tranh tài các nhân, `Kaggle Competitions` cũng cung cấp cơ hội để  thành lập team với những cá nhân khác. Cộng đồng của chúng tôi có rất phong phú các background và skills, nên mọi người có thể vừa cùng nhau làm vừa cùng nhau học hỏi kiến thức. Biết đâu bạn sẽ gặp bạn đại học hoặc đồng nghiệp trong tương lai trên Kaggle thì sao!

### 7. Áp dụng Machine Learning một cách chuyên nghiệp
Đây chính là lúc bạn tốn nhiều thời gian nhất với machine learning và cũng là lúc để bạn nâng cao nhất level của mình. Hãy bắt đầu từ việc chọn cho mình một vai trò mà mình mong muốn, sau đó xây dựng các projects liên quan đến nó để tạo nên portfolio cho mình. Nếu bạn chưa thực sự sẵn sàng cho phỏng vấn vị trí làm machine learning, thì hãy tự tạo một project mới ở vai trò hiện tại của bạn, sau đó tìm kiếm các cơ hội tư vấn, tham gia vào các cuộc thi hackathon và các cộng đồng cùng làm về ML sẽ là cơ hội tốt để bạn có thể không những cải thiện kỹ năng các nhân mà còn tìm kiếm được những mối quan hệ rất tốt trong lĩnh vực này. Các vị trí chuyên nghiệp thường chú trọng vào khả năng lập trình rất nhiều, nên việc tự nâng kỹ năng của mình lên với các project sẽ là một lợi thế rất lớn.

Những cơ hội lớn cho những chuyên gia machine learning:
- Áp dụng machine learning trên những hệ thống thực tế
- Tập trung vào nghiên cứu machine learning để nâng cao hơn nữa hiệu quả
- Cải thiện sản phẩm và đưa ra các quyết định business dựa trên phân tích và khai phá machine learning

### 8. Giúp đỡ và dạy người khác về machine learning
Dạy một người sẽ làm cho hiểu biết của bạn càng trở nên sâu sắc. Có rất nhiều cách để thực hiện điều đó, hãy chọn một cách phù hợp cho mình, ví dụ:
- Viết `research paper`
- Nói chuyện tại các hội thảo
- Viết blog và tutorial
- Trả lời câu hỏi trên Kaggle, Quora và những trang khác
- Hướng dẫn trực tiếp member
- Chia sẻ code trên Kaggel Kernels hay github
- Dạy một lớp học
- Viết một cuốn sách

Hi vọng các bạn có những cách nhìn rõ ràng hơn về Machine Learning và cách tiếp cận!

Nguồn: [Eight easy steps to get started learning artificial intelligence](https://www.forbes.com/sites/quora/2017/04/05/eight-easy-steps-to-get-started-learning-artificial-intelligence/#15ae0770b117)
