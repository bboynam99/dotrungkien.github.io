---
layout: post
title: "The Ethernaut: 17 - Locked"
mathjax: true
---

# 17. Locked 　 ★★★★★★

**Nhiệm vụ**: unlock contract

```js
pragma solidity ^0.4.23;

// A Locked Name Registrar
contract Locked {

    bool public unlocked = false;  // registrar locked, no name updates

    struct NameRecord { // map hashes to addresses
        bytes32 name; //
        address mappedAddress;
    }

    mapping(address => NameRecord) public registeredNameRecord; // records who registered names
    mapping(bytes32 => address) public resolve; // resolves hashes to addresses

    function register(bytes32 _name, address _mappedAddress) public {
        // set up the new NameRecord
        NameRecord newRecord;
        newRecord.name = _name;
        newRecord.mappedAddress = _mappedAddress;

        resolve[_name] = _mappedAddress;
        registeredNameRecord[msg.sender] = newRecord;

        require(unlocked); // only allow registrations if contract is unlocked
    }
}
```

## Phân tích

Trong hàm `register` có một lỗi cơ bản, đó chính là không có keyword `memory` trước biến `newRecord`, do đó biến `newRecord` sẽ chính là biến trong `storage` của contract.

Hay nói cách khác, `newRecord` đang trỏ đến slot của `unlocked`. Một chỉ dẫn rất rõ ràng phải không ?

Bạn có thể đọc thêm về storage trong smart contract tại [bài viết này](http://dotrungkien.github.io/2018/05/01/smart-contract-storage/) của mình.

## Solution

Ta chỉ cần chạy hàm `register`, vì kiểu của `_name` là bytes32, nên ta sẽ chạy với tham số là `_name=0x0000000000000000000000000000000000000000000000000000000000000001` để khi convert sang bool sẽ là `true`, phần `_mappedAddress` thì để địa chỉ bất kì là được

![png]({{site.utl}}/assets/images/locked.png)

Trên chrome console, kiểm tra lại xem đã được unlock chưa

```js
> await contract.unlocked()
true
```

Submit && all done!

![completed]({{site.url}}/assets/images/ethernaut-completed.png)

### Bình luận

Hãy luôn nhớ tới keyword `memory` khi khai báo một struct mới trong hàm.

Bên cạnh đó, việc nắm chắc _storage_ là điều tối cần thiết cho bất cứ ai muốn làm việc với _smart contract_ nói chung và _solidity_ nói riêng.

Enjoy coding!
