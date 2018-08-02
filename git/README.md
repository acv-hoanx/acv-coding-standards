# ACV Git flow

Flow tham khảo: [A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)

## Nguyên tắc
* Branch master - là branch chính sẽ release cho khách hàng, deploy production.
* Branch develop - là branch chính để phát triển, deploy test.
* 2 branch master và develop phải config protection 
    1. Require pull request reviews before merging
* Mỗi pull-request tương ứng với một feature issue.
* Mỗi 1 pull request feature thì chỉ có 1 commit code duy nhất
* Mỗi 1 pull-request fixbug không hạn chế số lượng commit
* Pull-request title phải đặt đúng quy định: `[issueType] #[ID issue] [Tit issue]` 

    （Ví dụ: `bug #WEPAPP-154 Tổng hợp bug trên web`）.
 
  Tuy nhiên với trường hợp 1 pull-request có chứa nhiêù commit thì cần phải ghi rõ trong nội dung pull request là trong commit đó xử lý đối ứng vấn đề gì.
  
        * Ví dụ:
            1. Pull-request title: `bug #WEPAPP-154 Tổng hợp bug trên web`
            2. Trong trường hợp pull-request có 2 commit thì nội dung pull request sẽ tương ứng như sau
                * `[issueType] #[ID issue] [Title issue]`
                * `[issueType] #[ID issue] [Title issue]`
                
* Đối với commit messages phải đặt đúng quy định: `[issueType] #[ID issue] [Nội dung issue]` 

    （Ví dụ: `bug #WEPAPP-154 Tổng hợp bug trên web`）.
* Tại môi trường local(trên máy lập trình viên), tuyệt đối không được thay đổi code khi ở branch master, develop. Nhất định phải thao tác trên branch khởi tạo để làm task.
* Trước khi push và tạo pull request bắt buộc phải rebase develop hoặc master để lấy code mới nhất.

    **Cách rebase.**
    1. Đối với trường hợp code xong mà chưa commit  push lên origin
        * Sau khi checkout branch feature mới và code xong rồi thì thực hiện add những file thay đổi vào stash
        * Thực hiện pull develop  rebase `develop` vào branch feature đang phát triển
        * Thực hiện apply stash đã lưu ở 1.1 (Nếu có conflict thì sửa)
        * Commit file thay đổi và push lên branch feature đó
        * Tạo pull request và assign cho người review là done.

    2. Đối với trường hợp code xong rồi commit và push lên origin mà quên ko rebase
        * Thực hiện pull develop và rebase `develop` vào branch feature đang phát triển
        * Nếu có conflict thì thực hiện resolve conflict (như hướng dẫn bên dưới)
        * Sau khi rebase xong thực hiện commit file có thay đổi đồng thời force push lên branch tương ứng.
* Đặt tên branch: 
  1. Mỗi 1 task thì đặt tên như sau: `feature/#[ID issue]`
        * Ví dụ: Task có id `WEPAPP-169` thì đặt tên branch như sau: `feature/#169`
        * **Lưu ý:** Đối với các bug phát sinh khi làm task thì fix luôn trong branch feature tương ứng
  2. Mỗi 1 task hotfix sau khi đã release thì đặt tên như sau: `hotfix/#[ID issue]`


## Quy trình

### Dự án trong giai đoạn develop
#### Vai trò của các branch
1. `master`
    * Luôn ở trạng thái có thể deploy.
    * first commit sẽ là base resource

2. `develop`
    * Được tách ra từ branch `master`
    * Nhánh sử dụng để phát triển và deploy cho test

3. `feature/#xxx`
    * Được tách ra từ branch `develop`
    * `xxx` là ID issue trên backlog
    * Dù chỉ có 1 commit cũng phải tạo pull request. 

4. `feature/#xxx-yyy` : chỉ sảy ra khi có 2 người làm 1 task hoặc nhờ người khác làm hộ
    * Được tách ra từ branch `feature/#xxx`
    * `yyy` là tên người làm
    * Dù chỉ có 1 commit cũng phải tạo pull request. Các pull request phải hướng về `feature/#xxx`.

5. `hotfix/#xxx` : Các bug phát sinh sau khi release production.
    * Được tách ra từ branch `master`
    * `xxx` là ID issue trên backlog
    * Sau khi làm xong cần tạo 2 pull request và hướng về `master` và `develop` rồi assign cho người review

#### Review
1. Sau khi code xong và push lên banch thì phải tạo Pull request để review
2. Sau khi tạo pull request xong thì phải assing pull request đó cho người review (Tất cả các pull request phải được review.)
3. Sau khi review xong. Nếu có yêu cầu phải sửa thì comment vào và assign ngược lại cho member.
4. Sau khi review xong. Nếu ok thì merge pull request đó và develop và deploy cho test

#### Deploy lên production (prd)
1. Sử dụng branch `master` chuyên để deploy lên production.
2. Sau khi đã phát triển xong thì tạo pull request và hướng về `master` để deploy lên production.
3. Xác nhận sự thay đổi, nếu không có vấn đề gì thì merge vào `master`. Lưu ý sau khi merge vào master thì tags version release để tiện quản lý
4. Sau khi merge xong thì deploy lên server prd.

#### Fix lỗi khẩn cấp
1. Các lỗi này cần phải fix ngay lập tức, thường thì không có thời gian để trải qua đủ các quy trình deploy
2. Khi hotfix cần làm theo đúng vai trò của branch hotfix


### Dự án trong giai đoạn phase 2nd

#### Vai trò của các branch

1. `master`
    * Là version mới khi release phase 1st
    * Ngoài ra tất cả đều giống như phần trên.

2. `develop`
    * Là version mới nhất để phát triển cho phase 2nd
    * Ngoài ra tất cả đều giống như phần trên.

3. `feature/#xxx`
    * Giống như phần trên.

4. `feature/#xxx-yyy` : chỉ sảy ra khi có 2 người làm 1 task hoặc nhờ người khác làm hộ
    * Giống như phần trên.

5. `hotfix/#xxx` : Các bug phát sinh sau khi release production.
    * Giống như phần trên.

6. `release` : Chỉ sử dụng khi phát triển phase tiếp theo và release theo feature
    * Sau khi phát triển xong 1 feature (test pass) thi cần checkout 1 branch release để chờ ngày release. Khi đó trên develop sẽ tiếp tục phát triển các feature tiếp theo
    * Trong trường hợp sau khi release mà có lỗi thì fix trên release. Sau khi fix xong thì tạo pull request để merge về develop để phát triển tiếp
    * Khi nào release feature lên production thì tạo pull request từ branch `release` hướng về master.
    * Trong trường hợp phát triển nhiều feature cùng 1 lúc thì có thể tạo nhiều branch `release` giống như feature.


### Khi xảy ra conflict trong quá trình rebase

Khi xảy ra conflict trong quá trình rebase, sẽ có hiển thị như dưới đây (tại thời điểm này sẽ bị tự động chuyển về một branch vô danh)
```sh
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: refs #1234 Sửa lỗi cache
Using index info to reconstruct a base tree...
Falling back to patching base and 3-way merge...
Auto-merging path/to/conflicting/file
CONFLICT (add/add): Merge conflict in path/to/conflicting/file
Failed to merge in the changes.
Patch failed at 0001 refs #1234 Sửa lỗi cache
The copy of the patch that failed is found in:
    /path/to/working/dir/.git/rebase-apply/patch

When you have resolved this problem, run "git rebase --continue".
If you prefer to skip this patch, run "git rebase --skip" instead.
To check out the original branch and stop rebasing, run "git rebase --abort".
```

1. Hãy thực hiện fix lỗi conflict thủ công（những phần được bao bởi <<< và >>> ）.
Trong trường hợp muốn dừng việc rebase lại, hãy dùng lệnh `git rebase --abort`.

2. Khi đã giải quyết được tất cả các conflict, tiếp tục thực hiện việc rebase bằng:

    ```sh
    $ git rebase --continue
    ```
