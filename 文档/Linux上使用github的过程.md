# Linux上使用Github的过程
## Git和GitHub环境的搭建
1. 安装Git `$ sudo apt-get install git`
2. 到GitHub上创建GitHub帐号
3. 生成SSH key。
    使用命令 `$ ssh-keygen -t rsa -C "your_email"`，your_email是你的邮箱地址，引号不能省略。
4. 复制 SSH。
    `~/.ssh`处查看SSH-key，`id_rsa` 为私钥，`id_rsa.pub` 为公钥。打开 id_rsa.pub 文件，将内容复制到剪贴板。
5. 回到github，进入Account Settings，左边选择SSH Keys，Add SSH Key，粘贴key。
6. `$ ssh -T git@github.com`测试SSH key是否成功，使用命令，如果出现`You’ve successfully authenticated, but GitHub does not provide shell access` ，这就表示已成功连上github。
7. `$ git config --global user.name "your name"`：配置Git的配置文件：配置用户名
    `$ git config --global user.email "your email"`：配置email

## 从GitHub克隆仓库到本地
1. 到GitHub的某个仓库，然后复制右边的那个（HTTPS clone url）
2.  `$ git clone <URL> <path>`回到要存放的目录下，使用命令，`<URL>`是你要克隆的GitHub项目URL，`path`是你要放这个repo的目录，不输入的话默认终端所在目录。
3. 要更新你的本地仓库至最新改动，执行：`$ git pull`

## 本地文件提交到GitHub

- `$ git status`可以查看本repository的状态信息，包括你有哪些东西是等待`add`/`commit`/`push`的。
- `$ git add <filename>`（指定文件名）或者`$ git add .`（不指定文件名）（"." 表示所有被改动的文件）。这是个多功能命令：可以用它开始跟踪新文件，或者把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。 将这个命令理解为“添加内容到下一次提交中”而不是“将一个文件添加到项目中”要更加合适。
- `$ git commit -m "<代码提交信息>"`：这是 git 基本工作流程的第一步；使用此命令以实际提交改动。现在，你的改动已经提交到了 HEAD，但是还没到你的远端仓库。如果某文件修改后没有`$ git add <file name>`，则此文件不会被Commit。
- `$ git commit -a -m '<代码提交信息>'`：加入`-a`选项可以跳过add步骤而直接把所有已被改动的跟踪中的文件提交。
- `$ gitk`可以打开git内建的GUI，对新手来说更加友好和直观的操作。
