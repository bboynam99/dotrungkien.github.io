---
layout: post
title: 'The X Y Problem'
---

## Vấn đề là gì?

_The X Y Problem_, hay _Vấn đề X Y_ là việc hỏi về một **giải pháp** bạn đang suy nghĩ thay vì hỏi về **vấn đề gốc**.

Hệ quả của nó chính là mất thời gian và công sức của cả đôi bên, cả người hỏi lẫn người được hỏi.

Ta có thể mô tả vấn đề này đơn giản như sau:

- A muốn làm X.
- A không biết làm thế nào để hoàn thành X, nhưng họ nghĩ là có thể thử phương pháp Y xem sao.
- Nhưng user cũng không biết làm Y luôn.
- A tìm kiếm sự giúp đỡ để giải quyết Y.
- B cố giúp A giải quyết Y, nhưng càng làm càng thấy rối vì có vẻ như Y là một vấn đề rất là lan man và kỳ quặc.
- Sau khi tốn rất nhiều thời gian công sức, cuối cùng thì vấn đề trở nên rõ ràng rằng A muốn giải quyết vấn đề X, và Y hoàn toàn không phải là một giải pháp cho X.
- Vấn đề này xảy ra khi mọi người bị mắc kẹt tại thứ mà họ đang nghĩ là giải pháp và không thể quay lại để giải thích từ đầu vấn đề một cách trọn vẹn.

## Giải quyết nó ra sao?

- Luôn cung cấp thông tin về bức tranh tổng thể, kèm theo đó là những giải pháp mà mình đã nghĩ tới.
- Nếu ai đó cần thêm thông tin, hãy cung cấp cho họ chi tiết nhất có thể.
- Nếu có một giải pháp nào bị bạn loại bỏ, hãy chia sẻ lý do mà bạn loại bỏ vấn đề đó. Nó sẽ đem lại thêm thông tin về yêu cầu của bạn.

## Ví dụ

### Ví dụ 1

Bạn T muốn lấy file extensions, nhưng thay vì hỏi đúng vấn đề, bạn ấy lại đi hỏi cách lấy 3 ký tự cuối của một chuỗi

> T: Làm thế nào tôi có thể lấy được 3 ký tự cuối của một chuỗi ?
>
> Kien: Nếu nó là một biến rồi thì chỉ đơn giản `echo ${foo: -3}` là được.
>
> Kien: À nhưng mà sao lại lấy 3 ký tự cuối làm gì ?
>
> Kien: Có phải cậu muốn lấy file extension?
>
> T: À đúng.
>
> Kien: Cậu nên biết rằng không phải lúc nào file extension cũng chỉ có 3 ký tự, nên việc cậu lấy 3 ký tự cuối đâu có giải quyết được vấn đề đâu (canloi).
>
> Kien: `echo ${foo##*.}`

### Ví dụ 2

Nếu đơn giản bạn C nói là "mình muốn không cho người khác biết thông tin OS của mình" thì câu chuyện đã đơn giản hơn rất nhiều.

> C: 'nmap -O -A 127.0.0.1' trả về message có 'OS:'. Làm cách nào tôi có thể thay thế nó?
>
> Kien: Xem trong source code của `nmap`, xem chỗ nào nó detect Linux, sau đó viết lại đoạn đó để nó không in ra nữa.
>
> C: Yeah, nhưng tôi chẳng biết chút nào về Linux api cả.
>
> Kien: Vốn fingerprint của `nmap` được xây dựng trên cách hoạt động của TCP/IP stack, thế nên thực ra không có phương phương án nào khác ngoài việc viết lại một phần trong stack đó.
>
> C: Tôi thực sự cần tránh message đó xuất hiện. iptables có thể làm điều đó không nhỉ?
>
> Kien: Vậy, đừng sử dụng OS detection hay version scanning nữa.
>
> C: Tôi muốn không cho người khác biết là mình đang sử dụng OS nào.

## Tham khảo

- [http://xyproblem.info/](http://xyproblem.info/)
- [https://mywiki.wooledge.org/XyProblem](https://mywiki.wooledge.org/XyProblem)
- [https://meta.stackexchange.com/questions/66377/what-is-the-xy-problem](https://meta.stackexchange.com/questions/66377/what-is-the-xy-problem)
