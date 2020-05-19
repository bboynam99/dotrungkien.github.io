---
layout: post
title: 'Multisig Wallet là gì?'
---

## Intro

Thông thường các ví trong blockchain nói chung đều được tạo ra bởi một private key duy nhất.

Tất cả các tài sản hay transaction đều được kiểm soát và ký bởi private key đó.

Điều này đối với cá nhân thì rất tốt, vì nó đảm bảo tính riêng tư và bảo mật, vì chỉ có chủ sở hữu private key mới có toàn quyền các tài sản đó.

Nhưng với một tổ chức, chẳng hạn như các tài sản sau khi ICO, hoặc tài sản chung của công ty thì khác, nếu tài sản này được kiểm soát bởi một người duy nhất thì sẽ có rất nhiều rủi ro có thể xảy ra như:

- người này chiếm tất cả tài sản rồi biến mất.
- do một vài lý do nào đó, người này đánh mất private key, thì việc khôi phục lại private key là không thể, đồng nghĩa với việc toàn bộ tài sản cũng sẽ không thể truy cập được.
- nếu không may người này mất đi, không ai có thể thừa kế hay sử dụng tài sản được nữa vì không có private key.

Để giải quyết các vấn đề trên, **Multisig Wallet** ra đời.

## Multisig wallet

Về bản chất _Multisig Wallet_ là một **smart contract** trên blockchain.

> _Multisig_ là viết gọn của _Multi Signature_ (đa chữ ký).

Một _Multisig Wallet_ có các tính chất:

- Các bên tham gia đồng thuận về một thứ gì đó
- Các quy tắc trong ví đã được xây dựng
- Ví có thể nhận tiền điện tử (ví dụ Ether)
- Ví có thể nhận request
- Ví có thể có khả năng xử lý request đó dựa trên đồng thuận

## Xây dựng Minimum Multisig Wallet

Sau đây ta sẽ xây dựng một ví dụ nhỏ để nắm được một Multisig Wallet có những phần nào và logic trong đó được triển khai ra sao.

Ngôn ngữ sử dụng ở đây là `Solidity`, phiên bản `0.5.0`.

Các bạn có thể lên [Remix](https://remix.ethereum.org/#optimize=true&evmVersion=null&version=soljson-v0.5.0+commit.1d4f565a.js) để gõ code trực tiếp mà không cần cài đặt môi trường gì.

### Immutable List of Owners

Mỗi Multisig Wallet đều có nhiều owner - là những người sẽ bỏ phiếu để đồng ý xem transaction có được phép diễn ra trên ví hay không

```js
pragma solidity 0.5.0;

contract MultiSigWallet {
    address[] public owners;  // immutable state

    constructor(address[] memory owners_) public {
        owners = owners_;
    }
}
```

### Off-Chain Consensus

Sự đồng thuận giữa các owner được thực hiện off-chain thông qua signed message. Các chữ ký này bao gồm 4 trường:

1. `destination`: đây là địa chỉ sẽ nhận Ether.
2. `value`: số lượng Ether được gửi.
3. `R, S, V`: chữ ký của message.
4. `nonce`: số nonce của transaction. Để tránh double spend, mỗi transaction với mỗi tài khoản được đánh một số duy nhất theo thứ tự, gọi là nonce.

Để transaction được thực hiện, tất cả các owners đều phải cung cấp một signed message (message đã được ký) để xác thực việc đồng ý chuyển `value` tới `destination`.

Khi đã có tất cả những _signed message_ này rồi, ta sẽ đưa vào hàm để thực hiện transaction:

```js
uint256 public nonce;

function transfer(
    address payable destination,
    uint256 value,
    bytes32[] calldata sigR,
    bytes32[] calldata sigS,
    uint8[] calldata sigV
)
    external
{
    bytes32 hash = prefixed(keccak256(abi.encodePacked(
        address(this), destination, value, nonce
    )));

    for (uint256 i = 0; i < owners.length; i++) {
        address recovered = ecrecover(hash, sigV[i], sigR[i], sigS[i]);
        require(recovered == owners[i]);
    }

    nonce += 1;
    destination.transfer(value);
}
```

## Các Multisig Wallet nổi tiếng

- [ConsenSys’ Multisig Wallet](https://github.com/ConsenSys/MultiSigWallet): Đây có thể coi là bản thực thi đơn giản nhất của Multisig Wallet, phiên bản solidity sử dụng cũng là 0.4.10 từ rất lâu rồi, nhưng có giá trị cực kỳ lớn, tại thời điểm viết bài thì trong ví này đang chứa gầm 80,000 Ether, tức khoảng 17 triệu đô. Các bạn có thể tra cứu ví này [tại đây](https://etherscan.io/address/0x851b7f3ab81bd8df354f0d7640efcd7288553419).

![]({{site.url}}/assets/images/multisig/consensys.png)

- [Gnosis’ Multisig Wallet](https://github.com/Gnosis/MultiSigWallet): là một phiên bản nâng cấp của Consensys Multisig Wallet, viết theo cấu trúc của Truffle project, viết test đầy đủ và thường xuyên được update. Tại thời điểm viết bài thì project github này vẫn đang được update.
- [BitGo’s Multisig Wallet](https://github.com/BitGo/eth-multisig-v2): cũng là một phiên bản viết theo cấu trúc của Truffle, được test đầy đủ và thường xuyên update. Điểm khác biệt ở đây chính là contract có thêm nhiều logic phức tạp hơn, một trong số đó là ERC20-Token Compatibility. Và wallet này triển khai phương thức ký 2-of-3, có nghĩa là có chính xác 3 bên tham gia, và cần 2 chữ ký để đồng ý cho một transaction được diễn ra.
- [Ethereum Dapp’s Multisig Wallet](https://github.com/ethereum/dapp-bin/blob/master/wallet/wallet.sol): Tương thích với Ethereum Wallet hay Mist, ta có thể deploy Multisig Wallet lên đây, và gọi trực tiếp hàm `send transaction` hay `confirm` một cách dễ dàng. Tuy nhiên có một nhược điểm là không hề có document, ta phải đọc code để biết thêm chi tiết.
- [Parity’s Multisig Wallet](https://parity.io/) (NOT RECOMMENDED): Đây cũng là một ví Multisig Wallet rất nổi tiếng trước khi nó [bị hack mất 300 triệu đô](https://medium.com/chain-cloud-company-blog/parity-multisig-hack-again-b46771eaa838) vào ngày 06/11/2017. Lý do bởi việc implement chưa tốt, và do đó nó không được khuyến khích sử dụng nữa.

![]({{site.url}}/assets/images/multisig/parity.png)

## Tham khảo

- https://medium.com/hellogold/ethereum-multi-signature-wallets-77ab926ab63b
- https://www.binance.vision/security/what-is-a-multisig-wallet
- https://programtheblockchain.com/posts/2018/07/11/writing-a-trivial-multisig-wallet/
- https://medium.com/@yenthanh/list-of-multisig-wallet-smart-contracts-on-ethereum-3824d528b95e
