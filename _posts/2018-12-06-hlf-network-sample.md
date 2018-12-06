---
layout: post
title: 'Hyperledger network sample'
---

![]({{site.url}}/assets/images/sample-network-hlf.png)

Có 4 tổ chức R1, R2, R3, R4 thiết lập một network Hyperledger Fabric.

Trong đó R4 đóng vai trò là initiator - người khởi tạo mạng. R4 sẽ không tham gia các vài trò bussiness trong network mà chỉ đơn thuần là admin của mạng mà thôi.

R1 và R2 cần có một kênh giao tiếp private riêng để tiến hành các bussiness. R2 và R3 cũng vậy.

Tổ chức R1 có một application thực hiện các bussiness transaction trên kênh C1.

Tổ chức R2 có một application thực hiện các bussiness transaction trên kênh C1 và C2.

Tổ chức R3 có một application thực hiện các bussiness transaction trên kênh C2.

Peer P1 maintain một bản copy của ledger L1 trên channel C1.

Peer P2 maintain một bản copy của ledger L1 trên channel C1 và một bản copy của ledger L2 trên channel C2.

Peer P3 maintain một bản copy của ledger L2 trên channel C2.

Network được hoạt động theo **policy rules** được định nghĩa trong **network configuration** NC4.

Network nằm dưới sự quản lý của các tổ chức R1 và R4.

Channel C1 hoạt động theo **policy rules** được định nghĩa trong **channel configuration** CC1.

Channel C1 nằm dưới sự quản lý của các tổ chức R1 và R2.

Channel C2 hoạt động theo **policy rules** được định nghĩa trong **channel configuration** CC2.

Channel C2 nằm dưới sự quản lý của các tổ chức R2 và R3.

Orderer O4 đóng vai trò phục như một network administration point cho toàn bộ network N, và nó sử dụng **system channel**.

Orderer O4 cũng support các **application channels** khác như C1 và C2, để đưa các transaction vào các block.

Mỗi một organizaitino đều có một CA - **Certificate Authority**.
