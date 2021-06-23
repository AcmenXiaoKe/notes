



# Git

## Git的基本工作流程

![image-20210520085153378](C:\Users\xiaoke\AppData\Roaming\Typora\typora-user-images\image-20210520085153378.png)

## Git的使用

### Git使用前的配置

在使用 git 前，需要告诉 git 你是谁，在向 git 仓库中提交时需要用到

1. 配置提交人姓名 git config --global user.name  提交人姓名
2. 配置提交人邮箱  git config --global user.name 提交人邮箱
3. 查看 git 配置信息 git config --list

**注意**

1. 如果要对配置信息进行修改，重复上面命令即可
2. 配置只需要执行一次

## 提交步骤

1. git init 初始化 git 仓库
2. git status 查看文件状态
3. git add 文件列表 追踪文件
4. git commit -m 提交信息 向仓库中提交代码
5. git log 查看提交记录

## 撤销

- 用暂存区中的文件覆盖工作目录的文件 git checkout 文件
- 将文件从暂存区中删除 git rm --cached 文件
- 将 git 仓库中指定的更新记录恢复出来，并且覆盖暂存区和工作目录  git reset --hard commitID

![image-20210520092229629](C:\Users\xiaoke\AppData\Roaming\Typora\typora-user-images\image-20210520092229629.png)

## 分支

> 为了便于理解，大家暂时可以认为分支就是当前工作目录中代码的一份副本。
>
> 使用分支，可以让我们从开发主线上分离出来，以免影响开发主线。

![image-20210520100216191](C:\Users\xiaoke\AppData\Roaming\Typora\typora-user-images\image-20210520100216191.png)

### 分支细分

1. 主分支( master ) ：第一次向 git 仓库中提交更新记录时会自动产生的一个分支

   ![image-20210520100509612](C:\Users\xiaoke\AppData\Roaming\Typora\typora-user-images\image-20210520100509612.png)

2. 开发分支( develop )：作为开发的分支，基于 master 分支创建

3. 功能分支( feature )：作为开发具体功能的分支，基于开发分支创建

**功能分支 -> 开发分支 -> 主分支**

### 分支命令

1. git branch 查看分支
2. git branch 分支名称 创建分支
3. git checkout 分支名称 切换分支
4. git merge 来源分支 合并分支
5. git branch -d 分支名称 删除分支 （分支被合并后才允许删除）（-D 强制删除）

### 暂时保存

> 在git中，可以暂时提取分支上所有的改动并存储，让开发人员得到一个干净的工作副本

临时转向其他工作。

- 存储临时改动:  git stash
- 恢复改动:  git stash pop
  



## Github

### 提交步骤

1. git push 远程仓库地址 分支名称

2. git push 远程仓库地址别名 分支名称

3. git push -u 远程仓库地址别名 分支名称

   > -u 记住推送地址及分支，下次推送只需要输入 git push 即可

4. git remote add 远程仓库地址别名 远程仓库地址

### 克隆仓库

- 克隆远端数据仓库到本地 git clone 仓库地址

拉取远程仓库中最新的版本

- 拉取远程仓库中最新的版本  git pull 远程仓库地址 分支名称

  在多人同时开发一个项目时，如果两个人修改了同一个文件的同一个地方，就会发生冲突，冲突需要人为解决

## Git忽略清单

将不需要被git管理的文件夹名字添加到此文件中，在执行git命令的时候，git就会忽略这些文件

git忽略清单的文件名称 .gitignore   (  一行一个文件或文件夹名称 )