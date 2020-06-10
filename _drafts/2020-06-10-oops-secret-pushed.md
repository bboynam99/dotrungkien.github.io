---
layout: post
title: 'Thôi xong, tui push secret key lên github rồi!!!'
---

## Câu chuyện

![]({{ site.url }}/assets/images/secret-pushed/git-push-go-home.jpg)

Một ngày đẹp trời, bạn hăng say cột dự án, công việc hoàn thành suôn sẻ, bạn gửi Pull Request và ra về.

Đang sửa soạn đi về, bỗng có tiếng kêu thất thanh từ phía leader: "dờ heo, đứa nào push secret key lên github thế này???????"

Từ git log, bạn hốt hoảng nhận ra chính mình chính là hung thủ, thôi xong, giờ làm sao đây ?

## Bình luận

Qua lời kể rất thật về một câu chuyện hư cấu, có lẽ chúng ta đều thấy thấp thoáng hình bóng của mình ở đây. Trong cuộc đời lập trình, có lẽ ai trong chúng ta cũng đã từng gặp những trường hợp như vậy, không chỉ một, có thể là nhiều lần.

Và không phải ai cũng may mắn.

Có rất nhiều trường hợp những sản phẩm đang phát triển hay thậm chí đang chạy rồi bị lộ secret key, lộ password, lộ api key hay nhiều thông tin nhạy cảm đã dẫn tới những thiệt hại hàng ngàn, thậm chí hàng triệu đô.

> Team mình cũng đã từng bị lộ, và thiệt hại khá lớn, xin phép không tiết lộ con số :(

Có thể bạn chưa biết, nhưng trên internet luôn có những tool chạy 24/24 chỉ để scan những vụ lộ key như vậy trên các nền tảng lưu trữ thông tin như github hay gitlab, bitbucket...
Nhanh tới mức ta có thể mất tiền sau **chưa đầy 1 phút** khi bị lộ key.

Và việc bảo vệ key an toàn luôn là việc phải đặt lên hàng đầu, từ _suy nghĩ_ (mindset) cho tới _hành động_ (actions).

## Actions

Lý thuyết thế đủ rồi, thế giờ ta lỡ đưa key lên rồi thì xử lý thế nào bây giờ ?

Hồi mới lập trình và làm quen với git, ta rất hay gặp cách xử lý thế này: Nhanh chóng xóa file secret đi, làm một commit mới rồi push lên ? coi như chưa có gì xảy ra cả.

Đây là cách xử lý hoàn toàn sai lầm :bigsmile: khi ta vào lịch sử commit, ta sẽ thấy file secret vẫn nằm nguyên trong commit cũ. Hi vọng là không còn ai trong chúng ta mắc lỗi sơ đẳng đó nữa. :smile:

Khi đã quen và có kinh nghiệm với git, ta biết rằng git lưu trữ lịch sử dạng cây, commit sau nối tiếp commit trước, có nghĩa là để thay đổi nội dung một commit, ta sẽ phải build lại toàn bộ lịch sử từ commit đó trở đi (nghe giống blockchain nhỉ).

Khi này ta có cách thứ hai: Sử dụng các tool để build lại lịch sử commit, đảm bảo rằng file secret không chứa trong bất kì commit nào, rồi push force lên repo.

Done, bài toán commit được giải quyết.

Nếu ta lỡ đưa các thông tin nhạy cảm lên trên github như secret key, api key, private key, SSH key, password, ta có thể xóa chúng khỏi lịch sử commit của repo.

Để xóa toàn bộ những file không mong muốn đã up lên github, ta có thể sử dụng lệnh `git filter-branch` hoặc sử dụng một tool open source có tên là `BFG Repo-Cleaner`.

Những lệnh và công cụ này giúp ta viết lại toàn bộ lịch sử commit. Do đó toàn bộ những hash của commit cũng sẽ bị thay đổi. Điều này có thể gây ảnh hưởng tới những pull request của repo.

Vì thế ta nên tiến hành xử lý bằng cách merge hoặc close toàn bộ các pull request trước khi tiến hành remove file để thay đổi lịch sử commit.

Ta có thể sử dụng `git rm` để xóa file khỏi commit gần nhất. Để tìm hiểu thêm về xóa thông tin khỏi commit gần nhất, ta có thể tham khảo thêm tại bài viết này [Removing files from a repository's history](https://help.github.com/en/articles/removing-files-from-a-repository-s-history).

## Xóa một file khỏi lịch sử commit

### Sử dụng `BFG Repo-Cleaner`

Chúng ta sẽ tiến hành download và cài đặt theo trang chủ tại đây: [https://rtyley.github.io/bfg-repo-cleaner/](https://rtyley.github.io/bfg-repo-cleaner/).

`BFG Repo-Cleaner` là một tool được xây dựng và maintain bởi cộng đồng open sources. `BFG Repo-Cleaner` cung cấp một cách xóa các file không mong muốn một cách nhanh chóng, đơn giản với nhiều tùy chọn.

Ví dụ ta có thể xóa những file không mong muốn (chứa các thông tin nhạy cảm) mà vẫn giữ lại được commit cuối cùng như sau:

```sh
bfg --delete-files YOUR-FILE-WITH-SENSITIVE-DATA
```

Hoặc để replace text bên trong file `password.txt` chẳng hạn, ta có thể sử dụng lệnh sau:

```sh
bfg --replace-text passwords.txt
```

`BFG Repo-Cleaner` cung cấp cho ta rất nhiều tùy chọn khác nữa, có thể tham khảo thêm tại [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)

### Sử dụng `git filter-branch`

Để minh họa về cách hoạt động của `git filter-branch`, ta sẽ làm một ví dụ xóa file chứa dữ liệu nhạy cảm trên một repo của chúng ta, và sau đó add file đó vào `.gitignore` để đảm bảo rằng sẽ không xảy ra việc lỡ commit một lần nữa.

## Ngăn chặn việc lỡ push thông tin nhạy cảm trong tương lai

## Tham khảo

- https://help.github.com/en/articles/removing-files-from-a-repository-s-history
- https://help.github.com/en/github/authenticating-to-github/removing-sensitive-data-from-a-repository
- https://git-scm.com/docs/git-filter-branch
- https://git-scm.com/book/en/Git-Tools-Rewriting-History
