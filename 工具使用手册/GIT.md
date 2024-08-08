# Git使用

## 配置Git环境

```bash
git config --global --list #查看git的配置信息
git config --global user.name "your github user name" #输入github的用户名
git config --golbal user.email "your email" #输入注册的邮箱
```
## 创建仓库
```bash
git init #初始化仓库，在当前目录下创建git仓库
git init testdir #在当前目录下，新建一个目录创建仓库
```

## 工作区，暂存区，本地仓库
工作区中代码--`git add`-->暂存区--`git commit`-->本地仓库

## 添加提交文件
```bash
git add abc.txt #件文件abc提交到暂存区
git add . #将所有文件提交到暂存区

git commit -m "第一次提交" #将暂存区的文件提交到本地仓库
```

## 回退版本
有三种方式回退，soft，hard，mixed
* soft：保留工作区文件，保留暂存区文件
* hard：不保留工作区文件，不保留暂存区文件
* mixed： 只保留工作区文件，不保留暂存区文件（默认）
```bash
git reset --hard 4nu2if4
git reset --soft 234rkjn  #后面接上版本号

git reset HEAD^
```
常用查询语句
```bash
git log --oneline  #显示提交日志
git status   #显示当前提交状况，可以看到暂存区中的文件
git ls-files   #查看暂存区
```

## 查看差异
```bash
git diff  #比较工作区和暂存区的文件内容
git diff HEAD  #工作区和HEAD指向的提交之间的差异（工作区和本地仓库比较）
git diff --cached  #比较暂存库和仓库的差异
git diff ID1 ID2 file1.txt  #比较两个版本id之间的差异（后面可以加上文件，这之查看这个文件的差异）
git diff HEAD~ HEAD #HEAD~表示最新版本的前一个版本，即当前版本和上一个版本之间比较
git diff HEAD~2 HEAD  #HEAD~2表示最新版本的前第二个版本
```
