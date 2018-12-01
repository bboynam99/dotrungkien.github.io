---
layout: task
title: Today i learned
permalink: /til
---

- sinh private key từ một chuỗi string & sinh ra một địa chỉ mới

```js
var utils = require('ethereumjs-util')

const toAddr = name => {
  const privateKey = utils.sha256(name)
  const address = utils.bufferToHex(utils.privateToAddress(privateKey))
```
