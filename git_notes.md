# Git 笔记
[toc]
## 创建工程
用命令
```git
git init
```
可以把当前目录转为`git`可以管理的仓库

### 添加文件
添加文件和更新文件一样

第一步,`git add`添加文件到当前仓库,例如
```git
git add readme.txt
```

第二步,用命令`git commit`告诉`Git`，把文件提交到仓库,例如
```git
git commit -m "wrote a readme file"
```
`-m`后面是本次提交说明

###删除文件
可以在本地删除文件,然后用`git status`查看状态,可以用
```language
git rm file.txt
git commit -m 'delete a file'
```
如果是误删了本地文件,可以从版本库里还原,
```language
git checkout --file.txt
```

### 查看当前文件状态
```language
git status
```
### 差别比较
```language
git diff readme.txt
```

### 返回之前版本状态
```language
git reset --hard HEAD^
```

### 查看改动版本情况
```language
git log
```

###查看每次输入的命令记录
```language
git reflog
```

##上传本地库到`GitHub`

###创建SSH Key

```language
ssh-keygen -t rsa -C "youremail@example.com"
```
如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。


###上传到云端
在`GitHub`网站上穿件`repository`,再将本地版本库与之关联,
```language
git remote add origin git@github.com:michaelliao/learngit.git
```
下一步，就可以把本地库的所有内容推送到远程库上：
```language
git push -u origin master
```

从现在起，只要本地作了提交，就可以通过命令：
```language
git push origin master
```
把本地master分支的最新修改推送至GitHub，现在，你就拥有了真正的分布式版本库！

##分支管理
###创建分支,切换分支
首先，我们创建dev分支，然后切换到dev分支：
```language
$ git checkout -b dev
Switched to a new branch 'dev'
```
`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：
```language
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```
然后，用`git branch`命令查看当前分支：
```language
$ git branch
* dev
  master
```
`git branch`命令会列出所有分支，当前分支前面会标一个*号。

现在，dev分支的工作完成，我们就可以切换回master分支：
```language
$ git checkout master
Switched to branch 'master'
```

###合并分支
现在，我们把dev分支的工作成果合并到master分支上(合并分支之前一定要回到主分支，否则合并无效!)：
```language
$ git merge dev
```

