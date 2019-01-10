---
layout: task
title: Today i learned
permalink: /til
---

### 20190110

sử dụng `debug` module thay cho `console.log`
rất đơn giản, khai báo

```js
debug = require('debug')('something');
```

tại nơi cần debug thì cột vào đó

```js
debug(needLogVariable);
```

sau đó khi test thì thêm biến môi trường vào

```js
DEBUG=something npm test
```

### 20190108

gặp lỗi `ModuleNotFoundError: No module named '_gdbm'`

lỗi này xảy ra khi thiếu thư viện `_gdbm.cpython-35m-x86_64-linux-gnu.so` tại python3.6, chỉ cần tạo symbolic link là được

chạy lệnh

```js
dpkg -L python3-gdbm
```

được kết quả

```js
/.
/usr
/usr/lib
/usr/lib/python3.5
/usr/lib/python3.5/lib-dynload
/usr/lib/python3.5/lib-dynload/_gdbm.cpython-35m-x86_64-linux-gnu.so
/usr/share
/usr/share/doc
/usr/share/doc/python3-gdbm
/usr/share/doc/python3-gdbm/copyright
/usr/share/doc/python3-gdbm/changelog.Debian.gz
/usr/share/doc/python3-gdbm/README.Debian
```

tạo symbolic link, câu lệnh `ln -s source destination` giống y như `cp`

```js
sudo ln -s /usr/lib/python3.5/lib-dynload/_gdbm.cpython-35m-x86_64-linux-gnu.so /usr/lib/python3.6/lib-dynload/_gdbm.cpython-36m-x86_64-linux-gnu.so
```

#### 2018

- Khi tạo channel trong fabric, nên nhớ chọn đúng version của images, crypto-config, cryptogentx, không thì có thể xảy ra lỗi liên quan đến endorsement hay permission.

- trong Hyperledger Fabric, mỗi `channel` sẽ có một `ledger` duy nhất. Mỗi peer sẽ maintain một bản copy của ledger này cho mỗi channel mà nó là memmber.

- sinh private key từ một chuỗi string & sinh ra một địa chỉ mới

```js
var utils = require('ethereumjs-util')

const toAddr = name => {
  const privateKey = utils.sha256(name)
  const address = utils.bufferToHex(utils.privateToAddress(privateKey))
```
