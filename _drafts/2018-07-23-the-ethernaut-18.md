---
layout: post
title: "The Ethernaut: 18 - Recovery"
mathjax: true
---

## 15. Naughty Coin 　★★★★★★

**Nhiệm vụ**: bạn có rất nhiều tiền, nhưng phải đợi tới tận 10 năm sau mới được tiêu số tiền đó. Bằng cách nào đó hãy tiêu hết số tiền đó mà ko cần phải chờ tới 10 năm nữa.

```js
pragma solidity ^0.4.18;

import 'zeppelin-solidity/contracts/token/ERC20/StandardToken.sol';

 contract NaughtCoin is StandardToken {

  string public constant name = 'NaughtCoin';
  string public constant symbol = '0x0';
  uint public constant decimals = 18;
  uint public timeLock = now + 10 years;
  uint public INITIAL_SUPPLY = 1000000 * (10 ** decimals);
  address public player;

  function NaughtCoin(address _player) public {
    player = _player;
    totalSupply_ = INITIAL_SUPPLY;
    balances[player] = INITIAL_SUPPLY;
    Transfer(0x0, player, INITIAL_SUPPLY);
  }

  function transfer(address _to, uint256 _value) lockTokens public returns(bool) {
    super.transfer(_to, _value);
  }

  // Prevent the initial owner from transferring tokens until the timelock has passed
  modifier lockTokens() {
    if (msg.sender == player) {
      require(now > timeLock);
      if (now < timeLock) {
        _;
      }
    } else {
     _;
    }
  }
}
```

### Phân tích

### Solution

### Bình luận

