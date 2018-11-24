---
layout: post
title: 'Vòng đời của một transaction trong Ethereum'
---

**Transaction** là trái tim của mạng Ethereum.

Trong [Yellow Paper](https://github.com/ethereum/yellowpaper) của Ethereum, **transaction** được định nghĩa như sau:

> Transaction: A piece of data, signed by an External Actor. It represents either a Message or a new Autonomous Object. Transactions are recorded into each block of the blockchain.

hiểu một cách đơn giản, toàn bộ các hoạt động chuyển tiền, nhận tiền, sinh contract, tạo token.... trên mạng ethereum đều là _transaction_. Những transaction này sẽ được ghi lại vào trong các _block_ và tồn tại mãi mãi không ai thay đổi được.

Trong bài viết này mình sẽ đi vào cách mà một transaction được tạo ra, cũng như các thông tin chứa trong nó. Nội dung bài viết chủ yếu được mình dịch lại từ bài viết [Life Cycle of an Ethereum Transaction](https://medium.com/blockchannel/life-cycle-of-an-ethereum-transaction-e5c66bae0f6e) của tác giả [mvmurthy](https://medium.com/@mvmurthy)

**Recommend**:

- Sẽ tốt hơn nếu bạn có kiến thức về ethereum blockchain
- Sẽ tốt hơn nếu bạn có kiến thức về solidity & web3js

# Một transaction xảy ra như thế nào

Mình sẽ lấy một contract về voting trong tutorial [Full Stack Hello World Voting Ethereum Dapp Tutorial](https://medium.com/@mvmurthy/full-stack-hello-world-voting-ethereum-dapp-tutorial-part-1-40d2d0d807c2) để các bạn dễ theo dõi. Ngoài ra nếu các bạn mới bắt đầu với phát triển dapp thì các bạn cũng có thể đọc qua tutorial đó trước khi vào bài này.

Mình sẽ không nhắc lại nội dung code cũng như cách deploy, mà mình sẽ đi thẳng vào sử dụng luôn.

Sau khi deploy, để tiến hành vote cho một ứng viên, ta sẽ tạo ra một **transaction** bằng cách gọi hàm như sau:

```js
Voting.deployed().then(function(instance) {
  instance
    .voteForCandidate('Nick', { gas: 140000, from: web3.eth.accounts[0] })
    .then(function(r) {
      console.log('Voted successfully!')
    })
})
```

transaction đó sẽ được broad cast đi toàn bộ các node trong mạng lưới Ethereum

![]({{site.url}}/assets/images/ethereum_network.png)

ta sẽ tiếp tục đi sâu vào nội dung của transaction này.

## Cấu trúc của một raw transaction object

Sau khi hàm `voteForCandidate` được gọi, đầu tiên nó sẽ được chuyển sang dạng **raw transaction** object bởi thư viện `web3js`.

**raw transaction** sẽ có cấu trúc dạng như sau:

```js
txnCount = web3.eth.getTransactionCount(web3.eth.accounts[0])
var rawTxn = {
  nonce: web3.toHex(txnCount),
  gasPrice: web3.toHex(100000000000),
  gasLimit: web3.toHex(140000),
  to: '0x633296baebc20f33ac2e1c1b105d7cd1f6a0718b',
  value: web3.toHex(0),
  data:
    '0xcc9ab2674e69636b00000000000000000000000000000000000000000000000000000000'
}
```

khá nhiều thông số, ta sẽ đi qua từng giá trị

- `nonce`: đây là một con số, nó chính là số transaction mà tài khoản gửi đã thực hiện. Mỗi lần gửi một transaction thì giá trị này sẽ tăng lên. Điều này nhằm ngăn các cuộc tấn công _replay attack_ trong ethereum.
- `gasPrice`: Mạng lưới ethereum được vận hành bởi _gas_, cũng giống như xăng để chạy xe vậy. Mỗi transaction đều tốn một lượng _gas_ nhất định, và gasPrice chính là đơn giá của 1 gas, đơn giá này tính bằng chính **ether**, tuy nhiên giá trị của nó khá nhỏ, chỉ từ 0.1 cho tới 100+ _Gwei_ mà thôi.
- `gasLimit`: Đây là số lượng gas tối đa mà người gửi có thể trả cho một transaction. Nếu coi một transaction là một chiếc xe máy, thì gas chính là xăng, gasPrice chính là giá xăng, còn gasLimit chính là dung tích bình xăng.
- `to`: địa chỉ gửi tới của transaction, nó chính là địa chỉ của contract voting.
- `value`: số lượng **ether** mà người gửi muốn chuyển.
- `data`: phần này khá phức tạp, về cơ bản nó sẽ được tạo ra từ 2 phần: **function signature** và **function parameter**
  - **function signature**: ethereum sử dụng 4 bytes đầu tiên trong mã hóa sha3 của function name để làm function signature, trong trường hợp này là `0xcc9ab267`.

```js
  > web3.utils.sha3('voteForCandidate(bytes32)')
  '0xcc9ab267dda32b80892b2ae9e21b782dbf5562ef3e8919fc17cab72aa7db9d59'
```

- **function parameter**: tham số trong hàm của ta là _Nick_, mã hóa bytes32 của giá trị này là `0x4e69636b00000000000000000000000000000000000000000000000000000000`

```js
> web3.utils.fromAscii('Nick')
0x4e69636b
```

ghép 2 giá trị lại ta sẽ được data như trên.

## Ký transaction

Bạn có thể để ý bên trên, transaction được gọi từ tài khoản web3.eth.accounts[0].

Trong mạng ethereum, để chứng minh transaction đó thuộc về mình, chúng ta sẽ phải ký giao dịch. Việc này được thực hiện với private key, trông giống như sau:

```js
const privateKey = Buffer.from(
  'e331b6d69882b4ab4ea581s88e0b6s4039a3de5967d88dfdcffdd2270c0fd109',
  'hex'
)

const txn = new EthereumTx(rawTxn)
txn.sign(privateKey)
const serializedTxn = txn.serialize()
```

## Xác nhận giao dịch tại local

Đầu tiên, tại local node sẽ validate transaction của chúng ta trước. Chỉ những transaction được đảm bảo chữ ký mới được phép broadcast tới các node khác trong mạng lưới.

## Broadcast transaction đi toàn bộ network

## Miner node chấp nhận giao dịch và đưa vào block

## Miner broadcast block đi toàn bộ network

## Local node nhận block

# Sử dụng metamask thay cho local node

# Offline signing

```

```
