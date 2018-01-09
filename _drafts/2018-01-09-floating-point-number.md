---
layout: post
title: "Bạn có thực sự hiểu về float ?"
mathjax: true
---

Từ xưa tới nay, biểu diễn số thực và các phép toán xung quanh nó luôn luôn là vấn đề khó khăn và đau đầu đối với các lập trình viên. Nó cũng là nguyên nhân dẫn đến những lỗi tưởng chừng như "trên trời rơi xuống" khi các kết quả thực trở nên sai lệch với những gì mong đợi. 

## Problem
Ngày hôm nay trong một topic technical của công ty, mình thấy có một lỗi rất thú vị như sau:
```mysql
mysql> select * from bad_ks;
+----+----------------+
| id | standard_value |
+----+----------------+
|  1 |       12419400 |
+----+----------------+
1 row in set (0.00 sec)

mysql> select * from bad_ks where standard_value = 12419355;
+----+----------------+
| id | standard_value |
+----+----------------+
|  1 |       12419400 |
+----+----------------+
1 row in set (0.00 sec)

mysql> select * from bad_ks where standard_value = 12419400;
Empty set (0.00 sec)

mysql> show create table bad_ks\G
*************************** 1. row ***************************
       Table: bad_ks
Create Table: CREATE TABLE `bad_ks` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `standard_value` float DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8
1 row in set (0.00 sec)

mysql> show variables like "version";
+---------------+--------+
| Variable_name | Value  |
+---------------+--------+
| version       | 5.7.19 |
+---------------+--------+
1 row in set (0.01 sec)
```

dafuq, rõ ràng `standard_value=12419355` sao lại thành ra 12419400 ? nguyên nhân do đâu ?

Vâng, nguyên nhân chính là đây:
> `standard_value` float DEFAULT NULL

12419355 nằm ngoài phạm vi của float, do vậy nó không được tính toán một cách chính xác.

Nguyên nhân liên quan khá nhiều đến cách máy tính biểu diễn số thực dấu phảy động (floating-point number), ta sẽ tìm hiểu thêm về điều này ngay sau đây, 

Tuy nhiên nếu bạn ngại phức tạp và nóng lòng muốn biết solution khắc phục, hãy bỏ qua phần này và đến luôn với phần cuối 5. Cách khắc phục của bài viết.

## Máy tính biểu diễn số thực như thế nào ?
Số thực được biểu diễn theo [Tiêu chuẩn IEEE_754-1985](https://en.wikipedia.org/wiki/IEEE_754-1985), bao gồm 2 loại: **single precision** và **double precision**

Level|Width|Range at full precision|Precision
---|---|---|---
Single precision|32 bits|±1.18×10−38 to ±3.4×1038|Approximately 7 decimal digits
Double precision|64 bits|±2.23×10−308 to ±1.80×10308|Approximately 16 decimal digits


## Tham khảo
- [IEEE_754-1985](https://en.wikipedia.org/wiki/IEEE_754-1985)
- [Single precision floating point format](https://en.wikipedia.org/wiki/Single-precision_floating-point_format)
- [Double precision floating point format](https://en.wikipedia.org/wiki/Double-precision_floating-point_format)
- [Mysql float type range and precision confusion](https://stackoverflow.com/questions/44339668/mysql-float-type-range-and-precision-confusion)
- [Floating point types](https://dev.mysql.com/doc/refman/5.7/en/floating-point-types.html)