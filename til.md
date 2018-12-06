---
layout: task
title: Today i learned
permalink: /til
---

- trong Hyperledger Fabric, mỗi `channel` sẽ có một `ledger` duy nhất. Mỗi peer sẽ maintain một bản copy của ledger này cho mỗi channel mà nó là memmber.

- sinh private key từ một chuỗi string & sinh ra một địa chỉ mới

```js
var utils = require('ethereumjs-util')

const toAddr = name => {
  const privateKey = utils.sha256(name)
  const address = utils.bufferToHex(utils.privateToAddress(privateKey))
```
