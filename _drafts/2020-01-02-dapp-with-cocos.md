---
layout: post
title: 'Xây dựng Dapp (Dgame) với Cocos Creator'
---

# Mở đầu

Trong thời đại blockchain đang rất phát triển, rất nhiều những ứng dụng truyền thống đang dần chuyển dịch sang hướng phi tập trung.

Những ứng dụng này được gọi là _decentralized application_, hay gọi tắt là **dapp**.

Dapp hiện giờ hầu hết được xây dựng dưới dạng web app, bởi môi trường và các công cụ tương tác đã được hoàn thiện khá tốt, ví dụ như _metamask_, hay các _dapp browser_.

Về mặt lý thuyết, ta cũng hoàn toàn có thể xây dựng được các dapp trên mobile, song hiện giờ vẫn còn một rào cản khá lớn khi các app store truyền thống như _google play_ hay _app store_ chưa chấp nhận các app như vậy. Một phần vì lý do bảo mật, một phần cũng vì sẽ ảnh hưởng đến hệ thống in-app purchase của các store đó.

Hi vọng trong tương lai với sự hoàn thiện hơn của blockchain thì các store sẽ có các cơ chế thông thoáng hơn với dapp.

Để xây dựng dapp chúng ta có thể sử dụng javascript thuần, hay reactjs, hay vuejs đều được, các bạn có thể tham khảo thêm các bài viết sau đây:

- Plain: [https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-gAm5y8LLldb](https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-gAm5y8LLldb)
- Reactjs: [https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-voi-reacjs-L4x5x8p15BM](https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-voi-reacjs-L4x5x8p15BM)
- Vuejs: [https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-voi-vuejs-vyDZOaP95wj](https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-voi-vuejs-vyDZOaP95wj)

Trong bài viết này mình sẽ hướng dẫn các bạn xây dựng một web dapp trên nển tảng ethereum bằng một engine game khá nổi tiếng **Cocos Creator**. Các bạn hoàn toàn có thể mở rộng nó để làm các web game tích hợp ethereum cho mình.

> Note: Môi trường làm việc trong bài viết này là MacOS, tuy nhiên vẫn có thể chạy trên Window bình thường.

## Cài đặt

Chúng ta cần cài đặt _truffle_ để code contract và _Cocos Creator_ để code một sample game đơn giản.

**truffle**

```sh
npm install -g truffle
```

**Cocos Creator**

Download và cài đặt tại đây: [https://www.cocos.com/en/creator](https://www.cocos.com/en/creator)

## Init project

Ứng dụng của chúng ta sẽ có 2 phần, phần `contract` và phần `game`, ta sẽ lần lượt tạo từng folder:

1. Tạo thư mục gốc của ứng dụng:

```sh
mkdir hello-world && cd hello-world
```

2. tạo thư mục `contract`:

```sh
mkdir contract && cd contract
```

3. init truffle project

```
truffle init
```

khi này chúng ta sẽ có được một project để bắt đầu code contract rồi.

![truffle-init]({{ site.url }}/assets/images/truffle-init.png)

4. init game project

Ta sẽ khởi động Cocos Creator lên và tạo một project mới, để project đơn giản và dễ hiểu nhất ta sẽ tạo project theo template "Hello-world"

![hello-world]({{ site.url }}/assets/images/hello-world.png)

## Contract Development

## Game Development

## Kết nối contract với game

## Mở rộng
