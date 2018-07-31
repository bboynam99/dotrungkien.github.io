---
layout: post
title: "The Ethernaut: 14 - Gatekeeper Two"
mathjax: true
---
## 14. Gatekeeper Two 　★★★★★★

**Nhiệm vụ**: vượt qua 3 cánh cổng khác và thay đổi địa chỉ của cửa vào

```js
pragma solidity ^0.4.18;

contract GatekeeperTwo {

  address public entrant;

  modifier gateOne() {
    require(msg.sender != tx.origin);
    _;
  }

  modifier gateTwo() {
    uint x;
    assembly { x := extcodesize(caller) }
    require(x == 0);
    _;
  }

  modifier gateThree(bytes8 _gateKey) {
    require(uint64(keccak256(msg.sender)) ^ uint64(_gateKey) == uint64(0) - 1);
    _;
  }

  function enter(bytes8 _gateKey) public gateOne gateTwo gateThree(_gateKey) returns (bool) {
    entrant = tx.origin;
    return true;
  }
}
```

### Phân tích

### Solution

### Bình luận
