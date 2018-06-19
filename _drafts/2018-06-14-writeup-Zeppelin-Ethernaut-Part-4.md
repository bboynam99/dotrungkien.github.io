---
layout: post
title: "Blockchain - hacking smart contract with Ethernaut CTF (Part 4)"
mathjax: true
---
Nightmare nerver ending!

Ethernaut mới gần đây đã cho ra đời thêm 4 thử thách nữa, và nhiệm vụ của chúng ta vẫn chỉ duy nhất là: vượt qua nó.

## 12. Privacy 　★★★★★★

**Nhiệm vụ**: unlock contract là xong.

```js
pragma solidity ^0.4.18;

contract Privacy {

  bool public locked = true;
  uint256 public constant ID = block.timestamp;
  uint8 private flattening = 10;
  uint8 private denomination = 255;
  uint16 private awkwardness = uint16(now);
  bytes32[3] private data;

  function Privacy(bytes32[3] _data) public {
    data = _data;
  }

  function unlock(bytes16 _key) public {
    require(_key == bytes16(data[2]));
    locked = false;
  }

  /*
    A bunch of super advanced solidity algorithms...

      ,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`
      .,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,
      *.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^         ,---/V\
      `*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.    ~|__(o.o)
      ^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'  UU  UU
  */
}
```

### Phân tích

- Để giải quyết được bài toán này các bạn phải hiểu được cách mà storage hoạt động trong solidity. Về cơ bản solidity sẽ nhóm các biến lần lượt theo thứ tự lại với nhau thành từng nhóm có độ lớn 256 bits để tối ưu hoá bộ nhớ. Nhìn qua các biến của contract:

```js
bool public locked = true;
uint256 public constant ID = block.timestamp;
uint8 private flattening = 10;
uint8 private denomination = 255;
uint16 private awkwardness = uint16(now);
bytes32[3] private data;
```

- như trên, ta sẽ có các nhóm: nhóm `locked, flattening, denimination, awkwardness`, nhóm `data[0]` (do bytes32 đã là 32 bytes = 256 bíts), nhóm `data[1]` và nhóm `data[3]` (ID là **constant** nên sẽ không được lưu trữ).
- vì thế `data[2]` sẽ có index là 2 trong storage của contract.

### Solution

- sử dụng hàm `web3.eth.getStorageAt()` để lấy ra `data[2]`

```js
web3.eth.getStorageAt(instance, 3, function (error,result) {alert(result); })
0x637a643557a340c479666c021cd18ee92449d19ff85bd81414bd52b54422cda5
```

- do hàm `unlock` chỉ cần `bytes16` thôi nên ta cắt đi một nửa độ dài đi và submit là xong (hoặc để cả cũng không sao)

```js
// nhớ thay bằng đoạn string mà bạn lấy được bên trên
await contract.unlock("0x637a643557a340c479666c021cd18ee9")
```

- kiểm tra lại tình trạng khoá của contract:

```js
await contract.unlocked()
false
```

- submit && all done!

### Bình luận
- Các bài liên quan đến **storage** của solidity luôn rất là rắc rối
- Các bạn có thể tham khảo thêm cách solidity lưu biến [tại đây](https://solidity.readthedocs.io/en/latest/miscellaneous.html#layout-of-state-variables-in-storage)
- Các bạn cũng có thể tham khảo cách decode các tham số trong hàm trong solidity [tại đây](https://medium.com/aigang-network/how-to-read-ethereum-contract-storage-44252c8af925)

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

## 12. Gatekeeper Two 　★★★★★★

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


## 12. Naughty Coin 　★★★★★★

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


## Conclusion

Vậy là chúng ta đã hoàn thành chuỗi bài *Blockchain - hacking smart contract with Ethernaut CTF*.

Hi vọng những kiến thức trong đó có thể giúp ích được các bạn ít nhiều trong việc viết code solidity nói riêng cũng như các ứng dụng phi tập trung nói chung.

Enjoy coding!

## References

- [The Ethernaut](https://ethernaut.zeppelin.solutions/)
- [Remix](https://remix.ethereum.org/)
- [Solidity Docs](http://solidity.readthedocs.io/en/develop/types.html#members-of-addresses)
- [Recommendations for Smart Contract Security in Solidity](https://consensys.github.io/smart-contract-best-practices/recommendations/#be-aware-of-the-tradeoffs-between-send-transfer-and-callvalue)