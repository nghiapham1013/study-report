# Git-flow là gì?

Git-flow là một plug-in (tool)

Nó là một plug-in giúp làm đơn giản hoá việc áp dụng branching model. Và Vincent Driessen đã gọi nó là "A successful Git branching model" khi ông viết blog của mình

Tư liệu tham khảo:

・　http://hm-solution.jp/lifehack/post2475.html

・　http://d.hatena.ne.jp/Yamashiro0217/20120903/1346640190

## Hình ảnh tổng quan về Git-flow, vai trò của mỗi branch

![](https://github.com/nghiapham1013/study-report/blob/201903-study-report/branch.png)

### master

Là branch dùng để release sản phẩm. Mỗi lần release sẽ đươc đánh release tag

### develop

Nhánh dành cho việc phát triển. Khi code đã chạy ổn và việc chuẩn bị cho release đã được hoàn thành, code từ nhánh `develop` sẽ được merge vào nhánh `master`. Trước khi release thì `develop` sẽ là nhánh có version mới nhất

### hotfixes 

Những thay đổi trực tiếp đến version hiện tại của sản phẩm (ví dụ như fix bug sau khi release) sẽ được thực hiện trên nhánh này. Code sẽ được check-out từ `master`, merge vào `master`, và gắn tag. Sau đó code sẽ được merge vào `develop`

### release branch

Nhánh dùng để chuẩn bị cho việc release sản phẩm. Vì được tách riêng khỏi việc thêm tính năng mới hay fix minor bugs, nên chúng ta có thể sử dụng nhánh này để lưu release code một cách clean nhất (code chưa dùng tới sẽ không bị lẫn vào đây trong khi việc phát triển tính năng mới vẫn đang được tiến hành bình thường)

Hầu hết các tính năng dự kiến sẽ release cũng như những bug fix đều đã có trong nhánh `develop` nên sẽ check-out từ `develop`. Khi việc chuẩn bị release đã hoàn thành thì code ở nhánh này sẽ được merge vào `master` và gắn tag. Sau đó sẽ merge trở lại `develop`

## 1. Clone project về local repository

Trường hợp đội phát triển sử dụng chung một repository thì hãy clone directory về và tạo ở local. Khi thực hiện clone thì hầu hết các dữ liệu đang được lưu trên server cũng sẽ được copy về local. Hay nói cách khác, lịch sử thay đổi của tất cả các file trong project sẽ được copy về local

Clone project về bằng command sau:

(sau `clone` là https url của GitHub)

~~~~
git clone https://github.com/.git
~~~~

Work directory sẽ được clone về local

** Nếu url theo sau `clone` không phải là HTTPS thì sẽ có lỗi sau xảy ra:

(SSH cũng sẽ bị lỗi)

~~~~
[vagrant@localhost ~]$ git clone git@github.com:SoneKosuke/pullreq.git
Initialized empty Git repository in /home/vagrant/pullreq/.git/
Permission denied (publickey).
fatal: The remote end hung up unexpectedly
~~~~

Trường hợp clone thành công sẽ có thông báo như sau:

~~~~
[vagrant@localhost ~]$ git clone https://github.com/SoneKosuke/pullreq.git
Initialized empty Git repository in /home/vagrant/pullreq/.git/
remote: Counting objects: 7, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 7 (delta 0), reused 3 (delta 0)
Unpacking objects: 100% (7/7), done.
~~~~

## 2. Init git-flow

Chúng ta có thể init local repository bằng cách chạy command dưới đây

Sau khi chạy sẽ được hỏi về tên các branch nhưng chúng ta có thể để mặc định cũng được. Cứ ấn Enter là xong

~~~~
git flow init
~~~~

Sau đó, hãy kiểm tra trạng thái của các branch

~~~~
git branch
~~~~

Kết quả sẽ như sau

~~~~
* develop
  master
~~~~

Nếu không có nhánh `*develop` thì trong remote repository sẽ không có data gì cả

Để cắt nhánh develop, ta thực hiện command sau

~~~~
git push origin develop 
~~~~

## 3. Tạo nhánh `feature` để thực hiện phát triển tính năng mới

Việc này thì chỉ cần một người trong team phát triển là có thể làm được

Trước khi triển khai việc phát triển tính năng mới thì chúng ta sẽ tạo nhánh `feature`. Việc phát triển tính năng mới cơ bản sẽ được thực hiện trên nhánh này

Ở ví dụ dưới đây, chúng ta sẽ tạo một feature branch mới với tên là `top`

~~~~
git flow feature start top
~~~~

Kết quả thực hiện:

~~~~
Switched to a new branch 'feature/top'

Summary of actions:
- A new branch 'feature/top' was created, based on 'develop'
- You are now on branch 'feature/top'

Now, start committing on your feature. When done, use:

     git flow feature finish top
~~~~

Sau đó hãy thử kiểm tra xem chúng ta đã tạo thành công hay chưa:

~~~~
git branch
~~~~

checkout ra từ nhánh `develop`, nếu nhánh `feature/top` được tạo và checkin thì có nghĩa chúng ta đã tạo thành công

~~~~
  develop
* feature/top
  master
~~~~

Với những câu lệnh chúng ta đã thực hiện ở trên thì việc thay đổi chỉ diễn ra ở dưới local. Chính vì thế, mọi thứ ở trên GitHub vẫn chưa được cập nhật

Để cập nhật thay đổi ở trên GitHub, hãy thực hiện lệnh sau:

~~~~
git flow feature publish top
~~~~

Kết quả:

~~~~
Already on 'feature/top'

Summary of actions:
- A new remote branch 'feature/top' was created
- The local branch 'feature/top' was configured to track the remote branch
- You are now on branch 'feature/top'
~~~~

Nếu muốn kiểm tra xem đã push thành công hay chưa, hãy kiểm tra ở trên GitHub

Hãy sử dụng nhánh này cho việc phát triển tính năng mới

![](https://github.com/nghiapham1013/study-report/blob/201903-study-report/branch%20list.png)

## 4. Bắt đầu quá trình phát triển. Pull code

Người phát triển cần phải sử dụng dữ liệu mới nhất trên remote repository để phòng việc xảy ra nhiều conflict

Với câu lệnh dưới đây, chúng ta sẽ có thể kéo code mới nhất của nhánh `feature/top` về local

~~~~
git flow feature pull origin top
~~~~

Kết quả hiển thị:

~~~~
Pulled origin's changes into feature/top.
~~~~

Nhánh `top` trên remote repository sẽ được kéo về. Dựa vào đó, nhánh `feature/top` của local sẽ được tạo ra

Sau đó hãy bắt tay vào phát triển tính năng mới!

## 5. Giai đoạn phát triển hoàn thiện. Thực hiện `add`, `commit`, `push`

Nếu giai đoạn phát triển đã hoàn thiện, có thể push code lên, thì hãy tiến hành `add`, `commit` code

Với câu lệnh dưới đây, `add` và `commit` sẽ đồng thời được thực hiện

(hãy đặt tên tag name sao cho phù hợp)

~~~~
git commit -am "tag name"
~~~~

Nếu muốn thực hiện `add` và `commit` riêng biệt thì hãy làm như sau:

(hãy đặt tên file name và tag name sao cho phù hợp)

~~~~
git add filename.php
git commit -m "tag name"        
~~~~

Sau khi `add` và `commit` xong, hãy thực hiện `push` code. Tất cả dữ liệu có trong local sẽ được đẩy lên trên GitHub

Đẩy nhánh `feature/top` đã được chỉnh sửa lên:

~~~~
 git push origin feature/top
~~~~

Màn hình hiển thị thông báo push thành công:
(ngoài ra cũng có thể kiểm tra xem đã push thành công chưa bằng cách xem trên GitHub)

~~~~
Counting objects: 4, done.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 285 bytes, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://SoneKosuke@github.com/SoneKosuke/git-flow-test.git
   1acc403..1c5c6ad  feature/top -> feature/top
~~~~

## 6. Trường hợp bị reject, push failed

Nếu bị reject hãy thực hiện rebase

Bằng việc rebase thì sẽ dựa trên những thay đổi để merge vào nhánh

Nếu vẫn chưa hiểu rõ về việc rebase hãy tham khảo bài viết sau:

http://liginc.co.jp/web/tool/79390#m1

Để thực hiện rebase, chúng ta chạy câu lệnh sau:

~~~~
 git pull --rebase origin feature/top
~~~~

Khi thực hiện `pull --rebase` thì sẽ có trường hợp xảy ra conflict

Nếu nội dung của 1 line bị thay đổi sẽ dẫn đến xung đột. Những điểm xảy ra conflict sẽ hiển thị ở trên GitHub


Trường hợp này, chúng ta phải resolve conflict bằng tay. Hãy nhớ confirm với các member khác về việc resolve conflict

Sau khi resolve conflict hãy thực hiện `git add` và `git rebase --continue`

~~~~
git add fizzbuzz.php 
git rebase --continue
~~~~

Sau khi rebase xong thì việc push coi như đã hoàn thành


## 7. Tạo pull request trên GitHub

Ví dụ, chúng ta truy cập GitHub và mở repository `pullreq` đã tạo trước đó. Thông tin về nhánh `update-readme` đã được push lên sẽ hiển thị

![](https://github.com/nghiapham1013/study-report/blob/201903-study-report/create%20pull%20page.png)

Các bạn sẽ nhìn thấy button `Compare & pull request`, click vào đó để đi đến trang thạo pull request

Tại trang tạo PR, hãy nhập đầy đủ thông tin để giải thích cho người nhận request (thường là người quản lý repository) về những thay đổi sẽ có trong PR này

![](https://github.com/nghiapham1013/study-report/blob/201903-study-report/PR%20open%20status.png)

Sau khi nhập mô tả, hãy kiểm tra lại ở phía dưới màn hình xem nội dung của PR có chính xác hay chưa. Nếu không có vấn đề gì, hãy ấn `Create pull request` để gửi PR

Như vậy là PR đã được gửi đi. Sau khi gửi thì một trang web mới sẽ mở và ở đó, PR vừa được tạo sẽ hiển thị ở trạng thái `Open`

## 8. Thực hiện merge pull request trên GitHub

Sau khi kiểm tra nội dung thay đổi có trong PR không có vấn đề gì, ta thực hiện merge PR

Việc merge và delete branch có thể thực hiện trên Git nhưng lần này chúng ta hãy thực hiện trên GitHub trước

Để thực hiện việc merge pull, chúng ta chỉ cần ấn button `Merge pull request` rồi đến `Confirm merge` là xong

![](https://github.com/nghiapham1013/study-report/blob/201903-study-report/merge%20pull.png)

![](https://github.com/nghiapham1013/study-report/blob/201903-study-report/confirm%20merge.png)

Sau khi merge, hãy xoá branch đó đi

![](https://github.com/nghiapham1013/study-report/blob/201903-study-report/delete%20branch.png)

Đến đây thì mình xin kết thúc bài viết ở đây. Hy vọng mọi người đã có một cái nhìn tương đối về Git-flow

*Bài viết tham khảo: https://qiita.com/KosukeSone/items/514dd24828b485c69a05*
