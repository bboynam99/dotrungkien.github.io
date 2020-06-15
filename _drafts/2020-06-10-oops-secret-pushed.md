---
layout: post
title: 'Thôi xong, tui push secret key lên github rồi!!!'
---

![]({{ site.url }}/assets/images/secret-pushed/git-push-go-home.jpg)

## Câu chuyện 1

Một ngày đẹp trời, bạn hăng say cột dự án, công việc hoàn thành suôn sẻ, bạn gửi Pull Request và ra về.

Đang sửa soạn đi về, bỗng có tiếng kêu thất thanh từ phía leader: "dờ heo, đứa nào push secret key lên github thế này???????"

Từ git log, bạn hốt hoảng nhận ra chính mình chính là hung thủ, thôi xong, giờ làm sao đây ?

## Câu chuyện 2

Dự án của công ty X lưu trữ trên một private repo, mọi người chỉ được phép fork về và tạo pull request. Mọi thông tin đều được giữ bí mật.

Một ngày nọ dev A xin nghỉ việc.

Để giữ lại những công sức code của mình, dev A âm thầm copy code của project về repo public của mình.

Cuối tháng công ty X bỗng thấy bị AWS charge 50 ngàn đô :wtf:

Công ty ráo riết đi tìm nguyên nhân, cuối cùng phát hiện ra key AWS nằm trong repo public của nhân viên A đã nghỉ kia. Cạn lời.

## Bình luận

Trong cuộc đời lập trình, có lẽ ai trong chúng ta cũng thấy hình bóng của mình thấp thoáng trong những câu chuyện trên, và không chỉ một, có thể là nhiều lần.

Và không phải ai cũng may mắn.

Có rất nhiều trường hợp những sản phẩm đang phát triển hay thậm chí đang chạy rồi bị lộ secret key, lộ password, lộ api key hay nhiều thông tin nhạy cảm đã dẫn tới những thiệt hại hàng ngàn, thậm chí hàng triệu đô.

> Team mình cũng đã từng bị lộ, và thiệt hại khá lớn, xin phép không tiết lộ con số :(

Có thể bạn chưa biết, nhưng trên internet luôn có những tool chạy 24/24 chỉ để scan những vụ lộ key như vậy trên các nền tảng lưu trữ thông tin như github hay gitlab, bitbucket...
Nhanh tới mức ta có thể mất tiền sau **chưa đầy 1 phút** khi bị lộ key.

Kể cả khi repo của chúng ta là private, thì nguy cơ vẫn còn từ chính yếu tố con người (câu chuyện 2).

Và việc bảo vệ key an toàn luôn là việc phải đặt lên hàng đầu, từ _suy nghĩ_ (mindset) cho tới _hành động_ (actions).

## Giải pháp

> Thế giờ làm thế nào để _không bị lộ key_ ?

Câu trả lời rất đơn giản: _không đưa key_ lên github hay bất kì nơi nào khác. Hãy giữ key offline và an toàn.

> Thế giờ ta lỡ đưa key lên rồi thì xử lý thế nào bây giờ?

Hồi mới lập trình và làm quen với git, ta rất hay gặp cách xử lý thế này: Nhanh chóng xóa file secret đi, làm một commit mới rồi push lên ? coi như chưa có gì xảy ra cả.

Đây là cách xử lý hoàn toàn sai lầm :bigsmile: khi ta vào lịch sử commit, ta sẽ thấy file secret vẫn nằm nguyên trong commit cũ. Hi vọng là không còn ai trong chúng ta mắc lỗi sơ đẳng đó nữa. :smile:

Khi đã quen và có kinh nghiệm với git, ta biết rằng git lưu trữ lịch sử dạng cây, commit sau nối tiếp commit trước, có nghĩa là để thay đổi nội dung một commit, ta sẽ phải build lại toàn bộ lịch sử từ commit đó trở đi (nghe giống blockchain nhỉ).

Khi này ta có cách hiệu quả hơn lúc trước: Sử dụng các tool để build lại lịch sử commit, đảm bảo rằng file secret không chứa trong bất kì commit nào, rồi push force lên repo.

Có vài thứ hỗ trợ ta điều này: lệnh `git filter-branch` hoặc sử dụng một tool open source có tên là `BFG Repo-Cleaner`.

Có một chú ý là do commit bị viết lại, nên toàn bộ các hash của chúng bị thay đổi. Điều này có thể gây ảnh hưởng tới những pull request hiện đang open trên repo.

Vì thế ta nên merge hoặc close toàn bộ các pull request trên repo trước khi tiến hành remove file để thay đổi lịch sử commit.

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

## Đưa file secret vào gitignore

```sh
$ echo "YOUR-FILE-WITH-SENSITIVE-DATA" >> .gitignore
$ git add .gitignore
$ git commit -m "Add YOUR-FILE-WITH-SENSITIVE-DATA to .gitignore"
> [master 051452f] Add YOUR-FILE-WITH-SENSITIVE-DATA to .gitignore
>  1 files changed, 1 insertions(+), 0 deletions(-)
```

## Đã đủ an toàn chưa ?

Ta đã xóa commit cũ, build lại commit history, file secret không còn tồn tại nữa, như vậy đã an toàn chưa?

Câu trả lời là CHƯA.

Thực tế github có một cơ chế, đó chính là `cached views`, dù trên repo không còn commit đó nữa, nhưng nếu có commit hash, ta vẫn có thể xem được như thường. Hoặc thông qua forks và pull request cũ ta vẫn có thể xem được toàn bộ thông tin của commit cũ, bao gồm cả file secret chúng ta đã push lên.

Toang!!!

Xử lý đống này thế nào ? Github suggest cho chúng ta phương án cuối cùng, đó chính là liên hệ với [GitHub Support](https://support.github.com/contact) và [GitHub Premium Support](GitHub Premium Support) để họ tiến hành xóa cached views cũng như toàn bộ những tham chiếu tới dữ liệu nhạy cảm trong các pull request trên github.

## What next ?

Có một điều nữa, hãy đảm bảo rằng những collaborators trong repo của ta khi update code sẽ update bằng `rebase`, chứ không phải bằng `merge`. Vì nếu dùng merge thì lịch sử cũ chứa commit có secret (trên máy của collaborator) sẽ được merge với lịch sử mới ta vừa build lại. Vậy là khi đưa lên mọi chuyện ta làm lúc trước sẽ thành công cốc.

Khi dùng `rebase`, lịch sử commit sẽ được đảm bảo là lịch sử mới bắt đầu từ commit ta build lại.

## Last step

Cuối cùng, khi đảm bảo mọi thứ chạy ổn định, ta sẽ remove tất cả những object backup mà ta tạo ra bởi `git filter-branch` ở trên, làm sạch git của chúng ta:

```sh
$ git for-each-ref --format="delete %(refname)" refs/original | git update-ref --stdin
$ git reflog expire --expire=now --all
$ git gc --prune=now
> Counting objects: 2437, done.
> Delta compression using up to 4 threads.
> Compressing objects: 100% (1378/1378), done.
> Writing objects: 100% (2437/2437), done.
> Total 2437 (delta 1461), reused 1802 (delta 1048)
```

## Kết luận

Tổng kết lại các bước xử lý như sau:

- Build lại lịch sử commit kể từ commit bị lộ secret
- Đưa file secret vào `.gitignore`
- Tiến hành `push force` để update lịch sử repo
- Nhắn các collaborators hãy tiến hành rebase lại
- Contact với [GitHub Support](https://support.github.com/contact) và [GitHub Premium Support](GitHub Premium Support) để xóa cached views cũng như toàn bộ references.
- Xóa rác

## Tham khảo

- https://help.github.com/en/articles/removing-files-from-a-repository-s-history
- https://help.github.com/en/github/authenticating-to-github/removing-sensitive-data-from-a-repository
- https://git-scm.com/docs/git-filter-branch
- https://git-scm.com/book/en/Git-Tools-Rewriting-History
