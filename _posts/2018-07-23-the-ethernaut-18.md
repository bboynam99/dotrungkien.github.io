---
layout: post
title: 'The Ethernaut: 18 - Recovery'
mathjax: true
---

# 18 Recovery 　 ★★★★★★

**Nhiệm vụ**: tác giả đã tạo ra contract `Recovery` đóng vai trò như một token factory để có thể dễ dàng tạo ra những đồng tiền ảo mới. Thật không may khi tạo ra đồng tiền ảo đầu tiên và gửi vào đó 0.5 ether để mua token, ông đã quên mất địa chỉ của token contract này. Nhiệm vụ của chúng ta là phải tìm ra địa chỉ của token contract đó và lấy lại 0.5 ether.

```js
pragma solidity ^0.4.23;

contract Recovery {

  //generate tokens
  function generateToken(string _name, uint256 _initialSupply) public {
    new SimpleToken(_name, msg.sender, _initialSupply);

  }
}

contract SimpleToken {

  // public variables
  string public name;
  mapping (address => uint) public balances;

  // constructor
  constructor(string _name, address _creator, uint256 _initialSupply) public {
    name = _name;
    balances[_creator] = _initialSupply;
  }

  // collect ether in return for tokens
  function() public payable {
    balances[msg.sender] = msg.value*10;
  }

  // allow transfers of tokens
  function transfer(address _to, uint _amount) public {
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    balances[_to] = _amount;
  }

  // clean up after ourselves
  function destroy(address _to) public {
    selfdestruct(_to);
  }
}
```

## Phân tích & Solution

Để tìm xem tác giả đã gửi tiền vào địa chỉ nào, rất đơn giản, ta paste địa chỉ contract lên trên [https://ropsten.etherscan.io](https://ropsten.etherscan.io), trong trường hợp của mình là [https://ropsten.etherscan.io/address/0x9998be07d49f52d93d98ebf78c7526d712246bf3#internaltx](https://ropsten.etherscan.io/address/0x9998be07d49f52d93d98ebf78c7526d712246bf3#internaltx).

Sau đó tìm tới phần `internal Txns`, ta thấy ngay transaction tạo ra token contract.

![png]({{site.url}}/assets/images/recovery.png)

Nhấn vào `Contract Creation`, ta thấy ngay được địa chỉ của token contract với 0.5 ether trong đó, trong trường hợp của mình là `0x417e544648E583Dc15004e86Ef94E1D79a068BFc`.

![png]({{site.url}}/assets/images/recovery-simpletoken.png)

Tiếp theo, tiến hành load `SimpleToken` với địa chỉ `0x417e544648E583Dc15004e86Ef94E1D79a068BFc` trên Remix IDE.

![png]({{site.url}}/assets/images/recovery-simpletoken-remix.png)

Trong contract `Simple Token` có hàm `destroy`:

```js
function destroy(address _to) public {
  selfdestruct(_to);
}
```

Trong solidity, khi ta gọi `selfdestruct(_to)` trong một contract, thì contract đó sẽ bị huỷ và chuyển toàn bộ tiền về cho địa chỉ `_to`.

Do đó ta sẽ tiến hành destroy contract `0x417e544648E583Dc15004e86Ef94E1D79a068BFc`, địa chỉ nhận là địa account của mình, để lấy lại 0.5 ether.

![png]({{site.url}}/assets/images/recovery-simpletoken-remix-destroy.png)

Quay trở lại địa chỉ của token contract trên etherscan.io, ta thấy balance của contract đã trở về 0, nghĩa là ta đã thành công.

![png]({{site.url}}/assets/images/recovery-simpletoken-completed.png)

Submit && all done!

![completed]({{site.url}}/assets/images/ethernaut-completed.png)

### Bình luận

Enjoy coding!
