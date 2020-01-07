---
layout: post
title: 'Xây dựng Dapp (Dgame) với Unity'
---

Trong bài này, chúng ta sẽ tiếp tục xây dựng một dapp (_decentralized application_) bằng unity.

Đây sẽ là một ứng dụng mobile.

> Note: Hiện giờ Apple Store và Google Play đang không chấp nhận các dapp mobile do tính bảo mật cũng như ảnh hưởng tới hệ thống in-app purchase của họ. Do vậy về mặt thực tiễn, làm dapp với unity sẽ không giúp bạn có thêm thu nhập từ app store :D

Đơn giản mình thích thì mình làm thôi!

Source code hoàn chỉnh: [https://github.com/dotrungkien/eth-unity-code-base](https://github.com/dotrungkien/eth-unity-code-base)

Các bài viết trước trong chuỗi bài "Xây dựng dapp":

- Plain: [https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-gAm5y8LLldb](https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-gAm5y8LLldb)
- Reactjs: [https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-voi-reacjs-L4x5x8p15BM](https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-voi-reacjs-L4x5x8p15BM)
- Vuejs: [https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-voi-vuejs-vyDZOaP95wj](https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-voi-vuejs-vyDZOaP95wj)
- Cocos Creator: [https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-voi-cocos-creator-63vKjk2bZ2R](https://viblo.asia/p/xay-dung-ung-dung-phi-tap-trung-dapp-voi-cocos-creator-63vKjk2bZ2R)

## Cài đặt

Chúng ta cần cài đặt _truffle_ để code contract, _Unity_ để code một sample app đơn giản.

**truffle**

```sh
npm install -g truffle
```

**Unity**

Download và cài đặt tại đây: [https://unity3d.com/get-unity/download](https://unity3d.com/get-unity/download)

## Init project

Ứng dụng của chúng ta sẽ có 2 phần, phần `contract` và phần `game`, ta sẽ lần lượt tạo từng folder:

1 - Tạo thư mục gốc của ứng dụng:

```sh
mkdir dapp-with-unity && cd dapp-with-unity
```

2 - tạo thư mục `contract`:

```sh
mkdir contract && cd contract
```

3 - init truffle project

```sh
truffle init
```

khi này chúng ta sẽ có được một project để bắt đầu code contract rồi.

![truffle-init]({{ site.url }}/assets/images/truffle-init.png)

4 - init game project

Ta sẽ khởi động Unity lên và tạo một project mới, đặt tên là `game`, đặt bên trong thư mục `dapp-with-unity`

OK ta đã sẵn sang để vào bước phát triển rồi.
