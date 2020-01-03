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

Chúng ta cần cài đặt _truffle_ để code contract và _Cocos Creator_ để code một sample game đơn giản, _Metamask_ để tương tác với web dapp.

**truffle**

```sh
npm install -g truffle
```

**Cocos Creator**

Download và cài đặt tại đây: [https://www.cocos.com/en/creator](https://www.cocos.com/en/creator)

**Metamask**

Cài đặt chrome extension tại đây: [https://chrome.google.com/webstore/search/metamask?hl=en-US](https://chrome.google.com/webstore/search/metamask?hl=en-US)

## Init project

Ứng dụng của chúng ta sẽ có 2 phần, phần `contract` và phần `game`, ta sẽ lần lượt tạo từng folder:

1 - Tạo thư mục gốc của ứng dụng:

```sh
mkdir hello-world && cd hello-world
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

Ta sẽ khởi động Cocos Creator lên và tạo một project mới, để project đơn giản và dễ hiểu nhất ta sẽ tạo project theo template `Hello-world`

![hello-world]({{ site.url }}/assets/images/hello-world.png)

OK ta đã sẵn sang để vào bước phát triển rồi.

## Contract Development

Trong bài này ta sẽ làm một contract vô cùng đơn giản, chỉ có 2 phương thức và 1 biến mà thôi.

### Contract

Vào thư mục `contracts` và tạo một file mới `SimpleStore.sol` với nội dung như sau:

```js
pragma solidity ^0.5.0;

contract SimpleStore {
  uint256 value;

  event NewValueSet(uint256 indexed _value, address _sender);

  function set(uint256 newValue) public {
    value = newValue;
    emit NewValueSet(value, msg.sender);
  }

  function get() public view returns (uint256) {
    return value;
  }
}
```

ở đây ta sẽ dùng phiên bản solidity 0.5.0.

### Truffle config

tại `truffle-config.js` ta sẽ config tất cả những thông tin về chain, compiler, tài khoản...

```js
module.exports = {
  contracts_build_directory: '../game/assets/Contracts',
  networks: {
    development: {
      host: '127.0.0.1',
      port: 8545,
      network_id: '123456789'
    }
  }
};
```

Ta sẽ build contracts vào luôn một thư mục bên trong thư mục game, để chút nữa ta có thể sử dụng nó trong Cocos.
Tại bước này, để đơn giản nhất ta sẽ sử dụng một local chain, chạy bởi `ganache-cli`.

### Chạy local chain

Nếu bạn chưa cài đặt `ganache-cli` thì hãy tiến hành cài đặt trước.

```sh
npm install -g ganache-cli
```

Ta sẽ chạy một local chain với chain id là _123456789_ bằng lệnh:

```sh
ganache-cli -i 123456789
```

Khi này đã có một chain chạy tại địa chỉ `http://localhost:8545` và 10 account, mỗi account có 100 ETH như thế này, mỗi account tương ứng với một private key bên dưới.

```sh
Available Accounts
==================
(0) 0x7c28281fb4fa65b40e3f47af4f88bfe82224090b
(1) 0x02c5c736031b4fa576cb911ddbb3e55eb741f134
(2) 0x905aef31f11b49923a6c377f83dec84567b90a25
(3) 0x3429059ea3d218c43266998cfed14e7f85f8d372
(4) 0x0ae76d77a96b34aff16f8910078ae2cf4e001768
(5) 0x148ffe781de6676fde20c2c6ff5bb022e80a9748
(6) 0x776be00b63a9d8692c1a4ce4d6baec1101fc913e
(7) 0x977eded9ac70b067c7cb840779ea9f46e8a89afb
(8) 0x01452fe9da1362ab8ace90d87056a4e5cfc22766
(9) 0xb422c3fafc0514cb733f83294d1681d6e3e8fc03

Private Keys
==================
(0) 26b3f59a6fec532ffc45f121bd2ba3c088666a34136669df03b315e938330d58
(1) 57adbbb3ba0aa454b3b85a87a6fc73e5ded11748e9323d0ba3866dca40e2e301
(2) db4093821ee1f362897847ac59851adea4a4aeeba22404321978111a02236174
(3) 3e953bb74802810739dec2df4cf2eb0e36d6ac665836a3c5658e35e4ef64814b
(4) 3ad55f2e89c7fe489a0cc04dd3574df69466656a98e92be78c9599a442370333
(5) ccc5585cbcbb0405fa5ea2aa48775e6ce7f75b819477c51396084b0b5ec3120d
(6) 77cab936b6001ad056cace50db94bacbec066fdf8842dbe598d7f656a18ec620
(7) 718159194d52ca85fad5dcaac0e9a9269994b6e3301cb233e107b4a71403d172
(8) debf1a6e65da5b3314de06dcefb6fec85065c9c74a42a8660a61d6786b322ba7
(9) 4c97648a754569ef7cfbfafeeb3df4213eea2fb8f1ced9ff38ade25e1ad688c5
```

hãy lưu lại một private key để chút nữa ta sẽ sử dụng account tương ứng trong metamask.

### Compile và Migration

Ta sẽ tiến hành compile contract để đảm bảo rằng contract của chúng ta không có lỗi:

```sh
truffle compile
```

kết quả

```sh
> Compiling ./contracts/Migrations.sol...
> Compiling ./contracts/SimpleStore.sol...
> Writing artifacts to ./../game/assets/Contracts
```

Để migration contract của chúng ta lên local chain, ta sẽ phải tạo thêm một file migration cho nó.

Trong thư mục `migrations` tạo thêm file `2_simple_store_migration.js` với nội dung như sau:

```js
const SimpleStore = artifacts.require('SimpleStore');

module.exports = function(deployer) {
  deployer.deploy(SimpleStore);
};
```

Và tiến hành migrate

```sh
truffle migrate --network development
```

kết quả

```sh

Starting migrations...
======================
> Network name:    'development'
> Network id:      123456789
> Block gas limit: 6721975


1_initial_migration.js
======================

   Deploying 'Migrations'
   ----------------------
   > transaction hash:    0xaa2af826be86a1561c79bbff42c1c8646921e327609995b486c0b71eb920edc4
   > Blocks: 0            Seconds: 0
   > contract address:    0x6E896215aD8E6c9d335AAc6642747Cfa96b88511
   > account:             0x7c28281Fb4FA65B40E3f47af4F88BFe82224090b
   > balance:             99.99430184
   > gas used:            284908
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.00569816 ETH

   > Saving artifacts
   -------------------------------------
   > Total cost:          0.00569816 ETH


2_simple_store_migration.js
===========================

   Deploying 'SimpleStore'
   -----------------------
   > transaction hash:    0x70d5bfd2b6f35bced155e93422f7f73a04f475f604e158b766421690ee730882
   > Blocks: 0            Seconds: 0
   > contract address:    0xaC78cEE4872Dd65945a22C66DFCB2d55a9cc65DC
   > account:             0x7c28281Fb4FA65B40E3f47af4F88BFe82224090b
   > balance:             99.99139726
   > gas used:            145229
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.00290458 ETH

   > Saving artifacts
   -------------------------------------
   > Total cost:          0.00290458 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0.00860274 ETH
```

OK vậy là xong phần contract.

## Game Development

## Kết nối contract với game

## Mở rộng

```

```
