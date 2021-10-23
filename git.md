# 版本控制

版本迭代

## 分类

1. 本地版本控制
2. 集中版本控制（svn）
3. 分布式版本控制（git）

# git配置

查看配置`git config -l`

查看不同级别的配置文件

```
# 查看系统config
git config --system --list

# 查案当前用户配置
git config --global --list
```

# 命令

创建本地仓库

```
git init
```

克隆远程仓库

```
git clone [url]
```

查看文件状态

```
# 查看指定文件状态
git status [filename]

# 查看所有文件状态
git status

# 添加所有文件到暂存区
git add .

# 提交暂存区的内容
git commit -m "message"


```



## 忽略文件

在主目录下建立.gitignore文件，规则如下

1. 忽略文件中的空行或以#开始的行会被忽略
2. 可以使用linux通配符
3. 如果名称前面有一个感叹号，表示例外规则，不被忽略
4. 如果最前面是路径分隔符，表示要忽略的文件在此目录下，子目录的文件不被忽略
5. 名称最后面是一个路径分隔符，表示要忽略的是此目录下该名称的子目录，而非文件

```
*.txt   #忽略所有.txt结尾的文件
!lib.txt #lib.txt除外
/temp   #仅忽略项目根目录下的todo文件，不包括其他目录temp
build/ #忽略build/下的所有文件
doc/*txt #忽略doc下的txt，但子目录中的txt不包括在内
```





生成公钥

ssh-keygen

# git文件操作

## 四种状态

1. Untracked：未跟踪，在文件夹中，但没有加入到git库，不参与版本控制，通过git add命令添加
2. Unmodify：文件已入库，未修改，版本库中的文件快照内容与文件夹中完全一致，如果被修改，则变为Modified，如果使用git rm移出版本库则成为Untracked
3. Modified:文件已修改，并没有进行其他操作，通过git add成为Staged或使用git checkout丢弃修改，返回Unmodify
4. Staged：暂存状态，执行git commit则将修改同步到库中，这是库中文件和本地文件一致，文件为unmodify状态，执行git reset HEAD filename取消暂存，文件状态为modified

# IDEA集成git

1、使用idea的版本控制新建一个本地仓库

2、使用命令推送到远程仓库

```
git remote add origin https://gitee.com/用户个性地址/HelloGitee.git
```

3、正常使用pull和push

（可以参照gitee或github的文档）

# git分支

## 分支常用指令

```
# 列出所有本地分支
git branch

# 列出所有远程分支
git branch -r

# 新建一个分支，但依然停留在当前分支
git branch [branch-name]

# 新建一个分支，并切换到该分支
git checkout -b [branch]

# 合并执行分支到当前分支
git merge [branch]

# 删除分支
git branch -d [branch-name]

# 删除远程分支
git push origin --delete [branch-name]
git branch -dr [r]
```

