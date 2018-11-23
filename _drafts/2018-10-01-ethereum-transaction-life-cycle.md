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

## Cấu trúc của một raw transaction object

## Ký transaction

## Xác nhận giao dịch tại local

## Broadcast transaction đi toàn bộ network

## Miner node chấp nhận giao dịch và đưa vào block

## Miner broadcast block đi toàn bộ network

## Local node nhận block

# Sử dụng metamask thay cho local node

# Offline signing
