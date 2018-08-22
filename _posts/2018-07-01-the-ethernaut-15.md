---
layout: post
title: "The Ethernaut: 15 - Naughty Coin"
mathjax: true
---
# 15. Naughty Coin 　★★★★★★

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

## Phân tích & Solution

Contract này được thiết kế theo chuẩn ERC20, nên ta hãy xem qua một lượt interface của chuẩn này:

```js
// ----------------------------------------------------------------------------
// ERC Token Standard #20 Interface
// https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md
// ----------------------------------------------------------------------------
contract ERC20Interface {
    function totalSupply() public constant returns (uint);
    function balanceOf(address tokenOwner) public constant returns (uint balance);
    function allowance(address tokenOwner, address spender) public constant returns (uint remaining);
    function transfer(address to, uint tokens) public returns (bool success);
    function approve(address spender, uint tokens) public returns (bool success);
    function transferFrom(address from, address to, uint tokens) public returns (bool success);

    event Transfer(address indexed from, address indexed to, uint tokens);
    event Approval(address indexed tokenOwner, address indexed spender, uint tokens);
}
```

Ta dễ dàng nhận thấy, ngoài `transfer` ra thì trong ERC20 còn có `transferFrom` nữa. Để chuyển token trong ERC20, chúng ta có 2 cách

- Cách1: thực hiện hàm `transfer`:

```js
function transfer(address to, uint tokens) public returns (bool success);
```

- Cách 2: `approve` cho ai đó quyền tiêu tiền của mình, sau đó người đó tiêu số tiền đó bằng `transferFrom`

```js
function approve(address spender, uint tokens) public returns (bool success);
function transferFrom(address from, address to, uint tokens) public returns (bool success);
```

Do đó ta sẽ khai thác như sau: Đầu tiên paste code lên Remix và load contract của ta ra, nhớ là sử dụng account *player* hiện tại nhé.

Bước 1: Trên chrome console kiểm tra balance hiện tại

```js
await contract.blanceof(player).then(x => x.toNumber())
1000000000000000000000000
```

Bước 2: Trên Remix, tiến hành `approve` cho một tài khoản khác với số lượng token là 1000000000000000000000000, ở đây mình sử dụng một tài khoản khác của mình là `0xc3A5e98871B9Bbb045A700c28b53C017D15Bd288`, các bạn hãy thay bằng tài khoản khác của các bạn.

![png]({{site.url}}/assets/images/naughty-coin-approve.png)

Bước 3: Switch qua account `0xc3A5e98871B9Bbb045A700c28b53C017D15Bd288` và tiến hành rút toàn bộ tiền tiền bằng `transferFrom`

![png]({{site.url}}/assets/images/naughty-coin-transferfrom.png)

Bước 4: Switch lại tài khoản player, trên chrome console check lại balance

```js
await contract.blanceof(player).then(x => x.toNumber())
0
```

Vậy là balance đã về 0. Submit && all done!

![completed]({{site.url}}/assets/images/ethernaut-completed.png)

## Bình luận

Đây là một bài toán khá đơn giản, điều cốt lõi ở đây chỉ là nắm và hiểu kiến trúc của ERC20 mà thôi.

Enjoy coding!