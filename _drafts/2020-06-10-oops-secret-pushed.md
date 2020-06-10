---
layout: post
title: 'Thôi xong, tui push secret key lên github rồi!!!'
---

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

## Ngăn chặn việc lỡ push thông tin nhạy cảm trong tương lai

## Tham khảo

- https://help.github.com/en/articles/removing-files-from-a-repository-s-history
- https://help.github.com/en/github/authenticating-to-github/removing-sensitive-data-from-a-repository
- https://git-scm.com/docs/git-filter-branch
- https://git-scm.com/book/en/Git-Tools-Rewriting-History
