---
layout: post
title: "Cách download số lượng lớn ảnh từ google images"
---

Bạn đang làm về machine learning ?

Bạn đang đau đầu vì thiếu dữ liệu ? 

Bạn search ra cả đống ảnh nhưng down từng cái về thì đến mùa quít mới xong ? những tool bạn tìm được thì lại quá lìu tìu ? 

Mình sẽ hướng dẫn các bạn một cách đơn giản để download ảnh với số lượng lớn search từ google images. 

Những ảnh này đều là ảnh nhỏ được google hiển thị trên trang tìm kiếm chứ không phải ảnh gốc, tuy nhiên với các bài toán Machine Learning và Deep Learning hiện tại thì với một kích cỡ nhỏ tầm 224x224 trở lên đã là quá đủ đối với các mạng Deep Neural Network lớn nhất hiện nay rồi.

> Fun fact: Có nhiều dữ liệu bao giờ cũng tốt hơn là lựa chọn thuật toán tốt.

Môi trường thực nghiệm:
- Python3.5
- MacOS

## Các bước tiến hành
Giả sử ta tìm dữ liệu về mèo, ta sẽ tiến hành như sau:
- **Step 1**: tìm kiếm ảnh google với từ khoá "cat"
![]({{site.url}}/assets/images/step1.png)



- **Step 2**: Bật Developer Tools lên (Cmd+Option+I hoặc View/Developer/Developer Tools), chọn tab **Network**
![]({{site.url}}/assets/images/step2.png)



- **Step 3**: Refresh trang, tại khung filter đánh vào **images?q=**, sau đó scroll trang. Bạn càng cần nhiều ảnh thì cần scroll càng nhiều.
![]({{site.url}}/assets/images/step3.png)


- **Step 4**: Chuột phải vào một ảnh, chọn **Copy/Copy All as cURL**
![]({{site.url}}/assets/images/step4.png)


- **Step 5**: Bật terminal lên, tạo một folder chứa ảnh, vào folder đó và chạy command sau:
```bash
pbpaste | awk '{print substr($0, 1, length($0)-1) ">>" NR ".jpg"}' | bash
```

**All done !**

Lưu ý nếu bạn đang thực hành trên ubuntu thì có thể sẽ không có lệnh pbcopy hay pbpaste, thay vì đó bạn có thể tự tạo alias cho mình bằng cách chỉnh sửa file ~/.bashrc như sau:

1. Thêm vào cuối cùng của file ~/.bashrc 2 dòng sau:
```js
alias pbcopy='xclip -selection clipboard'
alias pbpaste='xclip -selection clipboard -o'
```
2. Sau đó load lại bashrc bằng lệnh:
```js
source ~/.bashrc
```

rồi chạy lại bước 5 là xong.

## Lời kết
Việc download ở trên thực sự rất tiện dụng và nhanh, tuy nhiên bản thân google images cũng có rất nhiều ảnh lỗi nên khi download về rồi bạn vẫn sẽ phải loại bỏ bớt ảnh hỏng/ảnh thừa/ảnh không đạt tiêu chuẩn bằng tay. Việc này khá nhàm chán, nhưng đối với các bài toán Machine Learning thực tế thì điều này là một phần bắt buộc trong tiền xử lý dữ liệu, nên hãy làm quen với sự nhàm chán đó.

Nếu bạn biết cách làm tốt hơn, hãy chỉ cho mình nhé!

Enjoy coding!