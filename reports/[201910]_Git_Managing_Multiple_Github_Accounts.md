# [Git] A Practical Guide To Managing Multiple GitHub Accounts

<p align="justify">
Chào các bạn, nếu bạn là một software developer, trong công việc thỉnh thoảng bạn đã gặp trường hợp mà chúng ta làm nhiều dự án, sử dụng nhiều repository khác nhau và quan trọng là phải sử dụng nhiều github account khác nhau. Hay bạn đang làm việc trên một project cá nhân với tài khoản github cá nhân đồng thời cũng đang join một project của công ty với tài khoản github của công ty cung cấp.

Hôm nay mình sẽ chia sẻ về cách làm sao có thể quản lý nhiều Github account trên cùng một machine nhé.
</p>

## Create SSH keys
<p align="justify">
Việc đầu tiên chúng ta cần làm là tạo ra các SSH key riêng biệt. Open terminal và thực hiện câu lệnh sau để tạo và lưu ssh key.
</p>

```
$ cd ~/.ssh
$ ssh-keygen -t rsa -b 4096 -C "your_personal_email@domain.com"
  # save as id_rsa_personal
$ ssh-keygen -t rsa -b 4096 -C "your_work_email@domain.com"
  # save as id_rsa_work
```

<p align="justify">

Việc execute những command trên sẽ tạo ra `4` file trong thư mục `.ssh`
</p>

```
id_rsa_personal
id_rsa_personal.pub
id_rsa_work
id_rsa_work.pub
```

<p align="justify">
Chú ý rằng file có `.pub` extension là những public file mà chúng ta sẽ add vào Github account.
</p>

## Add SSH keys to Github account
<p align="justify">
Đầu tiên chúng ta sẽ copy public key vừa tạo
</p>

```
$ pbcopy < ~/.ssh/id_rsa_personal.pub
```

<p align="justify">

Truy cập vào GitHub, login và làm theo các bước sau để add public key vào Github account.

1. Click vào drop-down icon nằm bên cạnh avatar phía trên bên phải của navigation bar.
2. Click `SSH and GPC keys` trên navigation bar phía bên trái.
3. Click `New SSH key` button phía trên bên phải.
4. Thêm title để xác định key và paste puclic key vào `key` input. Sau đó click `Add SSH key` để hoàn thành.

Chú ý rằng trong bài viết này, mình đang giả sử có `2` github account (personal và work). Vậy nên chúng ta sẽ phải add public key tương ứng cho cả 2 github account này.
</p>

## Create configuration files to manage key
<p align="justify">

Quay trở lại `.ssh` folder và tạo một file cấu hình cho các key chúng ta vừa tạo
</p>

```
$ touch config
```

<p align="justify">

Mở và edit file `config`. Ở đây mình sử dụng: `$ nano config`
</p>

```
# Personal account - default config
Host github.com-personal
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa_personal
# Work account
Host github.com-work
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa_work
```

<p align="justify">

Để quản lý SSH key một cách tiện lợi hơn, chúng ta sẽ tạo hai file cấu hình`git`. Một file sẽ là `global git configuration` được sử dụng cho các personal project và file thứ 2 sẽ sử dụng cho các work project.

Mở terminal và thực hiện tạo glonal git configuration file với nội dung như sau:
</p>

```
$ cd ~
$ nano ~/.gitconfig
```

Content:
```
[user]
    name = John Doe
    email = johndoe@domain.com
[includeIf "gitdir:~/work/"]
    path = ~/work/.gitconfig
```

Tạo git config cho work:
```
$ nano ~/work/.gitconfig
```

Content:
```
[user]
    email = john.doe@company.com
```

<p align="justify">

Cấu hình ở phía trên sử dụng <a href="https://git-scm.com/docs/git-config#_conditional_includes">Conditional includes</a> được giới thiệu trong `git 2.13` để xử lý nhiều cấu hình khác nhau.

Để cấu hình `work` làm việc một cách chính xác, chúng ta sẽ giả sử tốn tại một thư mục có tên `work` chứa tất cả các project của công ty. Các bạn có thể thay đổi đường dẫn tùy ý.
</p>

<p align="justify">

Cuối cùng chúng ta sẽ thực hiện authent key với Github để đảm bảo rằng chúng ta đã câu hình đúng bằng cách sử dụng command sau:
</p>

```
$ ssh -T github.com-personal
  # Hi USERNAME! You've successfully authenticated, but GitHub does not provide shell access.
$ ssh -T github.com-work
  # Hi USERNAME! You've successfully authenticated, but GitHub does   not provide shell access.
```

<p align="justify">
Các bạn cũng có thể thực hiện commit, push code lên repository để kiểm tra việc cấu hình đã chuẩn chưa.

Cảm ơn các bạn đã theo dõi, hẹn gặp lại ở các bài viết tiếp theo.
</p>