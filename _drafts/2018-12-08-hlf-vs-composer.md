---
layout: post
title: 'Hyperledger - bắt đầu với Fabric hay Composer?'
---

# Introduction

Bạn mới bắt đầu với hyperledger và bạn băn khoăn không biết nên bắt đầu với `Hyperledger Composer` hay `Hyperledger Fabric`? bài viết này sẽ cung cấp cho các bạn một cái nhìn tổng quan về các ưu nhược điểm của 2 cách tiếp cận. Hi vọng sẽ giúp các bạn trong việc lựa chọn con đường phù hợp.

# TL;DR

> chọn Hyperledger Composer

# Tổng quan Hyperledger

Cả Hyperledger Fabric và Hyperledger Composer đều là các project nằm trong hệ sinh thái Hyperledger được phát triển bởi tổ chức **Linux Foundation**

- Hyperledger Fabric là một pluggable blockchain, nó cung cấp các một set các permissioned peer để truy cập vào một sổ cái chung.
- Hyperledger Composer là một set ở tầm cao hơn, sử dụng các API + tools để mô hình hóa, build, integrate và deploy blockchain network. Network này có thể được đóng gói và chạy trên Hyperledger Fabric.

Hay nói một cách đơn giản, Composer **chạy trên** Fabric. Composer cung cấp API bậc cao, và về bản chất, các API này vẫn gọi đến các API của Fabric.

Để có cái nhìn cụ thể hơn, ta sẽ đi vào so sánh việc code giữa Composer và Fabric thông qua một project sample của Hyperledger là [Marble Network](https://github.com/hyperledger/composer-sample-networks/tree/master/packages/marbles-network)

# Code với Fabric

> Có thể bạn chưa biết: Trong Fabric cũng có smart contract, gọi là `chaincode`

Các bạn có thể viết chaincode trong Fabric bằng `Go` hoặc bằng `Nodejs`, `Java`. Trong bài viết này ta sẽ dùng `Go`.

Một chaincode sẽ phải implement interface bao gồm 2 function: `Init` và `Invoke`.

Hàm `Init` sẽ được gọi mỗi khi chaincode được **instantiate** hoặc **upgrade** trong channel. Nó có dạng như sau:

```go
func (t *SimpleChaincode) Init(stub shim.ChaincodeStubInterface) pb.Response {}
```

Hàm `Invoke` sẽ được gọi mỗi khi ta muốn query dữ liệu hoặc tạo transaction trong Fabric.

```go
func (t *SimpleChaincode) Invoke(stub shim.ChaincodeStubInterface) pb.Response {}
```

Fabric không có phân biệt `call` (query) hay `send` (transaction) như trong Ethereum, mà tất cả đều là `Invoke`.

Ta sẽ phải tự check xem `Invoke` sẽ gọi hàm private nào thông qua tham số đưa vào function `stub.GetFunctionAndParameters()`, bằng `if`. Vâng, thủ công vô cùng =))


## Step 1: Data Model

Các blockchain nói chung đều lưu trữ dữ liệu tại một nơi gọi là *sổ cái phân tán* - distributed ledger.

Data model trong Go chaincode được định nghĩa dưới dạng Go **struct**, dưới dạng JSON data.

```go
type marble struct {
   ObjectType string `json:"docType"` //docType is used to distinguish the various types of objects in state database
   Name       string `json:"name"`    //the fieldtags are needed to keep case from bouncing around
   Color      string `json:"color"`
   Size       int    `json:"size"`
   Owner      string `json:"owner"`
}
```

## Step 2: Dispatching Incoming Calls

Mỗi khi client submit một transaction lên trên blockchain (trong Fabric ta sẽ dùng một node-sdk client), nó sẽ sử dụng một interface dạng RPC bất đồng bộ. RPC calls sẽ dispatch các transaction đó đến các hàm Go tương ứng để process. Như trên ta cũng có nói, việc này đơn giản là xử lý một loạt các `if` trong `Invoke`:

```go
func (t *SimpleChaincode) Invoke(stub shim.ChaincodeStubInterface) pb.Response {
   function, args := stub.GetFunctionAndParameters()
   fmt.Println("invoke is running " + function)
   // Handle different functions
   if function == "initMarble" { //create a new marble
      return t.initMarble(stub, args)
   } else if function == "transferMarble" { //change owner of a specific marble
      return t.transferMarble(stub, args)
   } else if function == "transferMarblesBasedOnColor" { //transfer all marbles of a certain color
      return t.transferMarblesBasedOnColor(stub, args)
   } else if function == "delete" { //delete a marble
      return t.delete(stub, args)
   } else if function == "readMarble" { //read a marble
      return t.readMarble(stub, args)
   } else if function == "queryMarblesByOwner" { //find marbles for owner X using rich query
      return t.queryMarblesByOwner(stub, args)
   } else if function == "queryMarbles" { //find marbles based on an ad hoc rich query
      return t.queryMarbles(stub, args)
   } else if function == "getHistoryForMarble" { //get history of values for a marble
      return t.getHistoryForMarble(stub, args)
   } else if function == "getMarblesByRange" { //get marbles based on range query
      return t.getMarblesByRange(stub, args)
   }

   fmt.Println("invoke did not find func: " + function) //error
   return shim.Error("Received unknown function invocation")
}
```

Sau đó trong mỗi function, Go code sẽ gọi methods trong `shim` để đọc hay ghi dữ liệu lên sổ cái chung. `shim` chính là phần đứng giữa chaincode và sổ cái, nó sẽ làm các nhiệm vụ CRUD state, emit các events..

Ví dụ:

```go
err = stub.DelState(colorNameIndexKey)
colorNameIndexKey, err := stub.CreateCompositeKey(indexName, []string{marble.Color, marble.Name})
stub.PutState(colorNameIndexKey, value)
valAsbytes, err := stub.GetState(name) //get the marble from chaincode state
```

ta sẽ đi sâu hơn vào detail của một function.

## Step3: Validate arguments

Mỗi function chỉ nhận vào một mảng bao gồm tên functino và các đối số. Vì thế việc thực hiện validate số lượng các đối số luôn là việc cần làm đầu tiên trong function.

```go
func (t *SimpleChaincode) transferMarble(stub shim.ChaincodeStubInterface, args []string) pb.Response {
   //   0       1
   // "name", "bob"
   if len(args) < 2 {
      return shim.Error("Incorrect number of arguments. Expecting 2")
   }
```

## Step4: Lookup Asset

Chaincode có thể tìm kiếm các asset trong mạng bằng `id`, và return một `error` nếu nó không tồn tại.

```go
marbleName := args[0]
newOwner := strings.ToLower(args[1])
fmt.Println("- start transferMarble ", marbleName, newOwner)
marbleAsBytes, err := stub.GetState(marbleName)

if err != nil {
   return shim.Error("Failed to get marble:" + err.Error())
} else if marbleAsBytes == nil {
   return shim.Error("Marble does not exist")
}
```

## Step5: Deserialize data

Nếu asset mà bạn tìm kiếm ở bước 4 tồn tại, nó sẽ trả về dưới dạng raw data, tức bytes, ta sẽ phải tiến hành deserialize dữ liệu đó ra và convert nó trở lại dạng Go struct.

```go
marbleToTransfer := marble{}
err = json.Unmarshal(marbleAsBytes, &marbleToTransfer) //unmarshal it aka JSON.parse()
if err != nil {
   return shim.Error(err.Error())
}
```

## Step 6: Update data in memory

## Step 7: Serialize data and persist

## Step 8: Content based query

## Step 9: Emitting events

# Coding with Composer

## Step 1: Data model

## Step 2: Dispatch incoming calls

## Step 3: Validate arguments

## Step 4,5,6,7 Lookup Asset, Deserialize Data, Update Data In Memory, Serialize Data and Persist

## Step 8: Content based query

## Step 9: Emitting events

# Testing

# Summary

# References
- [https://blog.selman.org/2017/07/08/getting-started-with-blockchain-development/](https://blog.selman.org/2017/07/08/getting-started-with-blockchain-development/)