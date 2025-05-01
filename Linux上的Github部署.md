# Linux 上的 Github 部署

### 写在前面

不管你是使用 WSL2 还是在导师实验室的服务器上跑代码，常常会遇到需要与他人协作的情况。这时候，你或许会想起 Github Desktop 这一便于协作，创建分支，进行 `push` 和 `pull` 的工具。但是 Github Desktop 并不能直接在 WSL2 或服务器上运行，这时候怎么办呢？我们需要在 Linux 上进行 Github 的部署。这篇小短文会教你怎么做。注意，我们不挑系统，无论你是 Winodws 还是 Mac ，都可以参考；毕竟部署的地方都是 Linux系统嘛。

### 从本地开始理解 github 协作

首先，你需要有一个 github 账号，然后创建一个仓库（repository）。这个仓库就是你项目的主体，你可以把它看作是一个文件夹，里面包含了你项目的所有文件。

然后，你需要在本地电脑上安装 git 软件，这个软件可以让你跟踪你的仓库，并与他人协作。安装好 git 之后，你就可以在命令行里输入 `git clone` 命令克隆这个仓库到本地。

```bash
git clone https://github.com/your-username/your-repository.git
```

克隆完仓库之后，进入仓库目录，并进行 `git` 初始化。
```bash
cd your-repository
git init
```
- **注意**： `git` 初始化的作用是生成一个 `.git` 目录，里面包含了仓库的版本信息，追踪你的改动，使之与远程仓库同步。

接着，你就可以在本地创建分支 `new-branch` ，在这个分支下编辑代码。
```bash
git branch new-branch
git checkout new-branch
```

在编辑完成之后，你可以查看你改动的地方。
```bash
git status
```

会显示类似这个的输出：
```bash
On branch new-branch
Your branch is up to date with 'origin/new-branch'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file1.txt
        modified:   file2.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

如果有新创建文件或者修改文件，就按照上方的指使写命令行，把你的改动提交到本地仓库。
```bash
git add file1.txt file2.txt
git commit -m "commit message"
```

- **注意**：这里的 `commit message` 就是你对这次改动的描述，可以写一些有意义的东西。

然后，再把你在本地分支的改动推送到远程仓库。
```bash
git push origin new-branch
```

你就可以把你的代码推送到远程仓库的某个分支 `target-branch` 。
```bash
git push origin new-branch:target-branch
```

- **注意**：这里出现的两个分支，一个是**本地分支**，一个是**远程分支**。一般在协作中，每个人有一个远程分支，这样大家可以互相查看对方的工作进展，方便的时候可以将之合并到 `main` 分支；而本地分支则是你自己的工作分支，你可以自己创建很多个，每一个干不同的事儿，你在这里编辑代码，然后推送到远程分支。

### 在服务器上部署

直观上，我们需要做的是将自己的账号和 git 关联。

首先，Github 上注册的账户信息写入。
```bash
git config --global user.name "yourGithubName" 
git config --global user.email "youremail@example.com" 
```

验证你的账户信息是否添加成功。
```bash
# 查看 git 账号信息
git config user.name
git config user.email

# 或者直接看整个账户信息
git config --list
```

配置你的公共密钥。公共密钥可以理解为你的身份证，你需要把它放到 Github 的 SSH 管理中，这样 Github 就可以识别你。
```bash
cd ~/.ssh
ls
# 在服务器上生成公钥
ssh-keygen 
```

进入 `~/.ssh` 目录，查看 `id_rsa.pub` 文件，复制里面的内容，将其纳入你的 Github 账号的 SSH 管理中。


测试链接是否成功。
```bash
ssh -T git@github.com 
```

出现以下提示就说明链接成功了:

```    
Hi YourGithubName! You've successfully authenticated, but GitHub does not provide shell access.
```

恭喜你，接着你就可以按本地的操作，在 Linux 上开启你的 Github 协作之旅啦！

### 注意
我们每次进行工作之前最好更新进展，方便同步！
```bash
git pull origin main
# 这里更新了 main 分支的最新内容，也可能更新某个协作者的 branch
```


#### 参考资料
- [CSDN文章](https://blog.csdn.net/Nismilesucc/article/details/122715949)
- [CSDN文章](https://blog.csdn.net/qq_47553403/article/details/120596743)
- Github 官方文档：[https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh)
