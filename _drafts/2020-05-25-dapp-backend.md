---
layout: post
title: 'Xây dựng backend cho Decentralized Application (ĐApp)'
---

## Intro

**Decentralized Applications**, hay ĐApps, là các ứng dụng được chạy trên các nền tảng phi tập trung.

Nó gắn liền với các khái niệm như _Distributed Ledger Technologies_ (DLT) hay _Blockchain_. Do đó khi nhắc tới _ĐApps_, ta thường mặc định rằng đó là các ứng dụng trên blockchain.

Do chạy trên các nền tảng phi tập trung, do đó nó cũng sẽ phải có những kiến trúc đặc biệt để đạt được tính bảo mật cũng như tính tinh cậy của hệ thống.

Trong bài bày chúng ta sẽ phân tích một số những yêu cầu về kiến trúc đối với _ĐApp_, đồng thời cũng đưa ra vài kiến trúc phổ biến mà các _ĐApp_ hiện nay đang sử dụng.

> ĐApp nói chung và Blockchain nói riêng rất rộng, trong bài viết này ta sẽ lấy ví dụ với những nền tảng ĐApp đang phổ biến nhất hiện nay là Ethereum, EOS, TRON. Tất nhiên những kiến thức này vẫn có thể áp dụng cho những nền tảng phi tập trung khác.

Một số điểm nổi bật:

- Lưu trữ private key tại backend một cách đảm bảo
- Cách design smart contract, xác định thứ gì cần `decentralized`
- kiến trúc Decentralized và Semin-Decentralized
- Giải quyết các vấn đề low-level như network load hay lỗi

## Decentralized Programs và Blockchain

Giá trị lớn nhất mà blockchain và các côn Máy Làm Bánh Mì Tự Động Panasonic SD-P104WRA g nghệ tương tự mang lại chính là việc trên đó ta có thể xây dựng được nhứng ứng dụng, mà không thể bị thay đổi hay làm giả một khi nó đã chạy. Hay nói cách khác, ta có thể xây dựng cázc ứng dụng chạy chính xác theo design mà không một ai có thể ảnh hưởng tới hành vi của ứng dụng.

Ethereum/EOS/TRON... hay những nền tảng tương tự đã cài đặt những runtime phức tạp, để ta có thể tiến hành lập trình và xây dựng ứng dụng trên đó. Chúng luôn chạy như những gì chúng được design, khôgn exception, và được bảo mật giống như bản thân nền tảng đó vậy.

## Decentralized Applications

Decentralized Applications - hay ĐApp là những ứng dụng bảo mật và không thể thay đổi chạy trên các mạng phi tập trung, kết hợp với các front-end và back-end truyền thống. Do đó, đôi khi không hoàn toàn 100% ứng dụng là decentralized, mà có thể là centralized một phần, hoặc semi-decentralized tuỳ vào bài toán.

![]({{site.url}}/assets/images/dapp-backend/backend.png)

Để hình dung một cách đơn giản ĐApp là gì, thì ta hãy nghĩ tới những hệ thống centralized đang hoạt động hiện nay như _Youtube_ hay _Facebook_, thì thay vì sử dụng password-protected centralized account thì ta sẽ sử dụng **crypto identity** thay cho nó.

**Crypto Identity** này chính là **Ví điện tử**. Trong ví điện tử này, thì mỗi identity sẽ được sinh ra bởi một private key, được lữu trữ offline dưới local, không ai có thể truy cập được identity ngoài người sở hữ privatekey. Lý do để sử dụng **Crypto Identity** chính là để cung cấp một phương thức bảo mật, mà không ai có thể làm giả thay thay thế được.

Hiện nay, việc tính toán và lưu trữ trên các nền tảng blockchain thông dụng như Ethereum, EOS, TRON vẫn còn rất hạn chế. Trong tương lai thì khi chúng được scale, thì ta sẽ có khả năng một cách toàn diện các ứng dụng fully-decentralized, bao gồm từ frontend, backend hay bất cứ thứ gì khác.

Tuy nhiên ở thời điểm hiện tại, vì những lý do hạn chế đó mà ta cần phải kết hợp rất nhiều những kiến trúc khác nhau, bao gồm cả những kiến trúc truyền thống và kiến trúc phi tập trung để giải quyết bài toán mà ta đang gặp phải. Cụ thể:

- Sử dụng một server để host backend hoặc frontend của ứng dụng
- Ta vẫn phải sử dụng một backend để tương tác với những hệ thống đã được xây dựng từ trước. Ta không thể luôn luôn đập đi làm lại tất cả mọi thứ được, đặc biệt là khi liên quan đến những business logic phức tạp và đang chạy ổn định.
- Khả năng lưu trữ của blockchain có giới hạn (do giới hạn của block size), do đó những dữ liệu lớn chúng ta vẫn phải lưu trữ tại những server truyền thống. Trên thực tế cũng đã có những decentralized storage như IFPS hay Filecoin, nhưng tính ổn định của những service này vẫn còn là dấu hỏi. Hơn thế nữa, chúng vẫn đang trong quá trình phát triển, có lẽ ta nên chờ thêm thời gian để chúng có thể trở thành những sản phẩm thực sự hoàn thiện.

Do đó tại thời điểm hiện tại, hầu như đối với bất kì hệ thống phi tập trung nào, ta vẫn phải xây dựng backend cho nó. Và trong bài viết này, ta sẽ bàn đến cách thiết kế backend sao cho hiệu quả.

## Decentralization and Tokens

Hầu hết những blockchain hay những ứng dụng xung quanh hệ sinh thái của nó đều hoạt động dựa trên một tứ gọi là **tokens**, có thể coi nó như là năng lượng để vận hành hệ thống vậy. Token đơn giản là một asset đã được lập trình bên trong hệ thống, vậy thôi.

![]({{site.url}}/assets/images/dapp-backend/token.png)

Thông thường, token được tạo ra và lưu trữ bởi _smart contract_ viết trên nền tảng phi tập trung như Ethereum chẳng hạn. Bằng việc sở hữu token, ta có thể sử dụng chúng tại những ứng dụng khác trong cùng hệ sinh thái blockchain, hoặc có thể trading để tạo ra những token khác. Điểm đặc trưng của token đó là nó hoạt động chính xác theo những logic đã được thiết kế trong _smart contract_ mà hoàn toàn không phụ thuộc vào một chủ thể tập trung nào.

> Có thể bạn chưa biết: Trong trò chơi nổi tiếng nhất trên Ethereum - Krypto Kitties, tất cả những con mèo trên đó cũng đều chỉ là token (ERC721) mà thôi.

## Backend for Decentralized Networks

Để xây dựng ứng dụng phi tập trung tương tác được với blockchain, client sẽ phải tương tác trực tiếp với _smart contract_ thông qua _JSON RPC API_, gọi tới một public node của blockchain như _Infura_ chẳng hạn. Tuy nhiên những API cung cấp bởi Infura rất là đơn giản và hạn chế, ta rất khó có thể triển khai được business logic trên đó. Nên trên thực tế, ta thường xuyên phải xây dựng một backend để xử lý các hạn chế đó, và do đó biến ứng dụng của chúng ta thành semi-decentralized.

Toàn bộ các tương tác đối với mạng phi tập trung ta có thể gói gọn lại trong 3 điểm:

1. **Read** state của network
2. **Send** transaction để thay đổi các state của network.
3. **Listen** network events.

Để implement được tốt những thứ trên, ta cần chú ý các điểm sau:

- Với Ethereum, thì event _không ổn định_. Có hàng tá lý do dẫn tới việc ta không thể nhận được event khi transaction xảy ra, như lỗi mạng, fetch quá nhiều event một lúc, event có thể biến mất hoặc bị thay đổi do network fork... Và để giải quyết những vấn đề này, ta cần phải xây dựng một cơ chế để sync event và đảm bảo độ tin cậy.
- Tương tự vậy, transaction trong Ethereum cũng _không ổn định_, có hàng tá lý do dẫn đến việc transaction thất bại như nonce sai, gas không đủ, chữ ký sai lệch, sai logic.... Ta cần những cơ chế recover hay resend lại các transaction đó.
- Security: thật khó để có thể đảm bảo 100% rằng private key trên backend không bao giờ bị lộ. Thay vì thế, ta có thể thiết kế các phương án phù hợp hơn, để cho dù có bị tấn công, thiệt hại vẫn là quá nhỏ so với công sức mà kẻ tấn công bỏ ra.

## Đapp Architecture

Chúng ta sẽ đi qua vài kiến trúc thiết kế cho các ứng dụng phi tập trung. Ở đây ta viết tắt ĐPlatform thay cho Decentralized Platform - tức những nền tảng phi tập trung như Ethereum/EOS/TRON.

### Client ⇔ ĐPlatform: fully decentralized applications

Với kiến trúc này thì client của chúng ta (browser hoặc mobile app) sẽ tương tác trực tiếp với nền tảng phi tập trung thông qua những wallet software như Metamask, Trust hay những ví phần cứng như Trezor hay Ledger. Ví dụ các ứng dụng như CryptoKitties, diễn đàn Delegate Call của Loom; hay những ví điện tử như Metamask, Trust, TRON wallet; hay các sàn giao dịch phi tập trung như Etherdelta, Kyber.

### ĐPlatform ⇔ Client ⇔ Back End ⇔ ĐPlatform: semi-centralized applications

Có lẽ sử dụng một backend ở giữa để tương tác với các nền tảng phi tập trung là một kiến trúc phổ biến nhất hiện nay.

Một ví dụ về đó là các sàn giao dịch nổi tiếng hiện nay như Bitfinex hay Poliniex hay Binance. Tất cả tiền ảo giao dịch trên các sàn này đều được lưu trữ trên một database truyền thống. Ta nạp tiền vào bằng cách chuyển tiền đến một địa chỉ xác định của sàn (ĐPlatform ⇔ Client), và khi rút tiền ra thì ta sẽ đặt lệnh và chờ phía backend sẽ chuyển lại tiền cho chúng ta (Back End ⬌ ÐPlatform). Và tất nhiên, tất cả những tương tác khác bên trong app đều là các tương tác giống như trong app truyền thống và không ảnh hưởng gì tới Đplatform cả (Client ⬌ Backend).

Một ví dụ khác nữa sử dụng kiến trúc semi-decentralized chính là [Etherscan.io](https://etherscan.io/): Ta có thể get tất cả những action liên quan đến ethereum tại đây như sync transaction, tức những action với ĐPlatform, nhưng bản thân trang web này được host trên một backend cố định, và cũng cung cấp các API/UI như những web truyền thống thông thường.

Ta sẽ lấy một ví dụ đơn giản để hiểu luồng của một giao dịch trên các hệ thống này xảy ra và được xử lý như thế nào:

![]({{site.url}}/assets/images/dapp-backend/network-sample.png)

Tại đây:

1. Ta lắng nghe một event trên network bằng cách polling liên tục.
2. Một khi bắt được sự kiện, ta sẽ tiến hành những business logic và publish một transaction để thực hiện các logic đó.
3. Ta sử dụng private key để ký giao dịch
4. Sau khi transaction được gửi đi, ta tiếp tục polling network để check status của nó.
5. Nếu transaction tốn quá nhiều thời gian mà vẫn chưa được verify, có thể do rất nhiều lý do ta đã nói ở trên, trong trường hợp này ta sẽ publish bằng cách ký lại giao dịch & gửi với lượng gas cao hơn để có thể được verify sớm hơn.
6. Cuối cùng ta nhận được giao dịch đã được mined. Tại đây ta có thể tiếp tục thực hiện những bussiness logic của mình.

## ĐApp Back End

1. **Read** state của network
2. **Send** transaction để thay đổi các state của network.
3. **Listen** network events.

### Lắng nghe Network Events

Bởi tất cả các action bên trên mạng phi tập trung đều là bất đồng bộ, các transaction tốn những khoảng thời gian khác nhau thì mới được mine, nên để nắm bắt được trạng thái của các transaction một cách đầy đủ và kịp thời, ta phải sử dụng đến lắng nghe event.

Ví dụ như với một contract ERC-20 thì tất cả những transaction chuyển tiền đều sẽ phát ra event `Transfer`. Trong ứng dụng, ta cần lắng nghe event này để thực hiện các business logic của app, như gửi notification, email, hoặc chỉ đơn thuần là cập nhật lại số dư cho một tài khoản nào đó.

Như ta cũng đã có nói bên trên thì event trong mạng phi tập trung là không ổn định, có rất nhiều trường hợp ta không thể lắng nghe được trực tiếp event. Do đó ta cần xây dựng một backend để có thể thực hiện việc sync các event một cách hiệu quả hơn.

Tuỳ vào từng bài toán mà sẽ có cách thiết kế khác nhau, ta sẽ đưa ra một thiết kế ví dụ như sau để có thể cải thiện được cách lắng nghe trực tiếp event bằng sử dụng _message bus_

![]({{site.url}}/assets/images/dapp-backend/message-bus.png)

## Tham khảo

- https://en.wikipedia.org/wiki/Decentralized_application
- https://www.freecodecamp.org/news/how-to-design-a-secure-backend-for-your-decentralized-application-9541b5d8bddb/
