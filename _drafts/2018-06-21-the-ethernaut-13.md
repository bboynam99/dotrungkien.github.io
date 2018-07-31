---
layout: post
title: "The Ethernaut: 13 - Gatekeeper One"
mathjax: true
---
## 13. Gatekeeper One 　★★★★★★

**Nhiệm vụ**: vượt qua 3 cánh cổng và thay đổi địa chỉ của cửa vào

```js
pragma solidity ^0.4.18;

contract GatekeeperOne {

  address public entrant;

  modifier gateOne() {
    require(msg.sender != tx.origin);
    _;
  }

  modifier gateTwo() {
    require(msg.gas % 8191 == 0);
    _;
  }

  modifier gateThree(bytes8 _gateKey) {
    require(uint32(_gateKey) == uint16(_gateKey));
    require(uint32(_gateKey) != uint64(_gateKey));
    require(uint32(_gateKey) == uint16(tx.origin));
    _;
  }

  function enter(bytes8 _gateKey) public gateOne gateTwo gateThree(_gateKey) returns (bool) {
    entrant = tx.origin;
    return true;
  }
}
```

### Phân tích

- Ta sẽ phân tích cách vượt qua từng cửa một

```js
modifier gateOne() {
  require(msg.sender != tx.origin);
  _;
}
```

- cửa số 1: điều kiện `msg.sender != tx.origin` có thể vượt qua rất dễ bằng cách sử dụng một contract khác để gọi hàm

```js
modifier gateTwo() {
  require(msg.gas % 8191 == 0);
  _;
}
```

- cửa số 2: đây chính là điều kiện khó nhằn nhất của bài, ta phải có kiến thức về debug contract để vượt qua nó. Trong bài này ta sẽ sử dụng Remix IDE để thực hiện.


### Solution

### Bình luận

