---
layout: post
title: "Đánh version cho phần mềm như thế nào?"
---
Khi làm dự án, chúng ta chẳng lạ gì với việc thường xuyên phải deploy/build sản phẩm. Tuy nhiên có một điều khá quan trọng mà ít người để ý, đó chính là việc đánh version cho bản build. Có phải bạn đã từng nghĩ rằng "đặt version từ 0.0.0 rồi mỗi lần build thì tăng lên 0.0.1 là được?", nếu bạn vẫn đang suy nghĩ như vậy, có thể bài viết này sẽ có ích cho bạn.

## Semantic versioning
Việc đánh version cho sản phẩm rất quan trọng, nó không chỉ đơn thuần là một số để phân biệt phiên bản này với phiên bản khác, mà còn mang rất nhiều ý nghĩa trong đó. Người ta gọi cách đánh version đó là **Semantic Versioning**.

Một version sẽ có dạng **MAJOR.MINOR.PATCH**, ví dụ 0.0.4, 1.2.3, 2.5.6,.. Khi ta tăng mỗi giá trị trong đó thì:
- **MAJOR**: tăng giá trị này lên khi tạo ra những thay đổi không tương thích với phiên bản cũ (incompatible).
- **MINOR**: tăng giá trị này lên khi thêm những chức năng mới vào phiên bản cũ, tuy nhiên vẫn tương thích với phiên bản cũ (backwards-compatible).
- **PATCH**: tăng giá gì này lên khi fix bugs.

đằng sau **PATCH** có thể có thêm label nữa như *rc*, *beta*, ta sẽ nói đến sau. Trước hết ta chỉ quan tâm đến phần chính thôi.

Trong **Semantic Versioning**, chúng ta sẽ có các yêu cầu sau:
1. Phần mềm dùng Semantic Versioning phải khai báo một public API
2. Một phiên bản thông thường phải có dạng X.Y.Z Trong đó X, Y, Z là các số tự nhiên không âm, và không có số 0 đằng trước. X là major version, Y là minor version và Z là patch version. Các phần tử sẽ tăng dần, ví dụ 1.9.0 -> 1.10.0 -> 1.11.0.
3. Mỗi version được release thì nội dung của nó không được phép sửa đổi nữa. Nếu có sửa đổi, bắt buộc phải đánh một version mới.
4. **Major version** 0 (các phiên bản 0.y.z) sẽ là phiên bản khởi đầu của quá trình phát triển.
5. Version 1.0.0 định nghĩa public API.
6. **Patch version** Z (x.y.Z | x > 0) tăng lên khi fix bugs tương thích với phiên bản cũ.
7. **Minor version** Y (x.Y.z | x > 0) tăng lên khi tạo ra những thay đổi mới tương thích với phiên bản cũ. Hoặc khi một chức năng bị deprecated. Patch version sẽ được reset về 0 khi mà minor version tăng lên.
8. **Major version** X (X.y.z | X > 0) tăng lên khi tạo ra những thay đổi không tương thích với phiên bản cũ. Minor version và patch version sẽ được reset về 0 mỗi khi major version tăng lên.
9. <to be continued...>
## Kết luận

## Tham khảo
- [Software Versioning](https://en.wikipedia.org/wiki/Software_versioning)
- [Semantic Versioning](http://semver.org/)
