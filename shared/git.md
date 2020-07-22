# git指令分享

## 将本地项目上传至git
1. git init  &emsp;*把这个文件夹变成Git可管理的仓库*
2. git status &emsp;*显示工作区和暂存区的状态*&emsp;*可省略*
3. git add . &emsp;*将所有修改添加到暂存区*
4. git commit -m "对于本次提交的描述" &emsp;*将暂存区修改内容提交至本地仓库*
5. git remote -v &emsp;*查看本地仓库和哪些远程仓库相连接*&emsp;*可省略*
6. git remote add origin **git仓库地址** &emsp;*本地仓库与远程仓库连接*
7. git push -u origin master &emsp;*将本地的master分支推送到origin主机，同时指定origin为默认主机，后面就可以不加任何参数使用git push了。不带任何参数的git push，默认只推送当前分支，这叫做simple方式。下次再从本地库上传内容的时候只需: **git push origin master***

## 克隆远程代码
* git clone **仓库地址**  
    克隆远程仓库地址
* git clone -b **分支名** **仓库地址**  
    克隆该仓库某个分支的代码

## 查看提交历史 
* git log  
    查看提交记录（结果按提交时间倒序排列）
* git log --pretty=oneline  
    简洁的展示提交记录
* git reflog   
    查看包括回滚操作的历史记录（可以查看所有分支的所有操作记录，包括已经被删除的 commit 记录和 reset 的操作）
* git log --graph  
    以图形的方式展示
* git branch -v   
    当前分支最后一次提交的记录

## 分支 
* 常用指令
    - git branch  
        查看当前所处分支，以及有哪些分支
    - git branch **test**   
        创建test分支
    - git checkout **test**  
        换到test分支上
    - git checkout -b **test**  
        创建test分支，并切换到test分支上
    - git checkout -b **test** origin/**test**  
        创建test分支并切换至test分支（远程已存在test分支，在本地创建该分支）
    - git branch -m **oldName**  **newName**   
        修改分支名
    - git branch -d **test**  
        删除test分支（1.删除不掉当前所处的分支2.新创建的修改过文件但是没有合并过的分支，用此命令也删除不掉）
    - git branch -D **test**  
        强制删除新创建的没有合并过的分支
    - git push origin --delete **test**  
        删除远程分支
* 合并分支
    - 例如将分支B上的修改合并到分支A上：
    -    1. git checkout A  
        切换至A分支
         2. git merge B   
            合并B分支
         3. git branch B  
            将B分支删除（可省略）
        

## 删除文件与回退 
* git checkout -- **文件名**  
    将工作区的修改还原成上次提交时的样子（或者刚克隆完的样子，或者刚把它放入工作目录时的样子）,对于已经提交到暂存区的修改，无法丢弃
* git rm **文件名**  
    删除文件，并将删除的文件纳入暂存区
* git reset HEAD **文件名**  
    把暂存区的修改撤销掉，重新放回工作区
* git reset --hard HEAD^  
    向上回退一个版本（有几个^就向上回退几个）
* git reset --hard **历史版本号**  
    回退到指定历史版本
* git commit --amend  
    有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了,就可以进行如下操作,将第二次提交代替第一次提交  
    > 1. git commit -m '**描述**'
    > 2. git add **文件名**  
    > 3. git commit --amend

## 标签 
* git tag 
    查看当前存在的标签
* git tag **标签名**  
    轻量级标签
* git tag -a **标签名** -m '**描述**'  
    轻量级标签
* git show **标签名**  
    标签信息和与之对应的提交信息
* git tag -a **标签名** **提交记录**  
    后期打标签
* git tag -d **标签名**  
    删除标签

## 保存现场 
在研发过程中，经常会遇到处理任务过程中，被更高优先级的任务打断，最早使用的方法是手动把文件进行保存，后来使用git add 和git commit把文件保存在git本地，但是这样的方法都会有很大的缺陷。保存文件，会出现很多的多余文件。而临时提交可能会引起git pull时的冲突，以及误提交的情况。那如何解决这个问题呢？
* git stash  
    保存当前的工作现场，这时候就可以切换到其它分支进行工作，或者在当前分支上，完成其它紧急工作。
* git stash save "**描述**"  
	执行存储时，添加备注，方便查找
* git stash list  
    查看所有stash的内容
* git stash apply  
    恢复该分支上最新一次保存。stash记录不会被删除，需要通过git stash drop stash@{0}手动删除
* git git stash drop stash@{0}  
    手动删除stash记录
* git stash pop   
    恢复该分支上最新一次保存，并删除记录
* git stash apply "stash@{0}/@{1}"
    恢复该分支上名为@{0} /@{1}  的保存。stash记录不会被删除，需要通过git stash drop stash@{0} /@{1}手动删除

## 帮助 
git help

## git add 相关 
* git add .   
    不加参数默认为将修改操作的文件和未跟踪新添加的文件添加到git系统的暂存区，注意不包括删除
* git add -u .  
    -u  == --update ，表示将已跟踪文件中的修改和删除的文件添加到暂存区，不包括新增加的文件，注意这些被删除的文件被加入到暂存区再被提交并推送到服务器的版本库之后这个文件就会从git系统中消失了。
* git add -A .    
    -A == --all  ， 表示将所有的已跟踪的文件的修改与删除和新增的未跟踪的文件都添加到暂存区。
* git commit -am  "**描述**"  
    add和commit 合并操作,但是如果有新增文件，无法add
* git add -h  
    查看帮助，使用git add --help可以查看更详细的内容


## 补充知识
* ctrl+e  
    光标调到最后一位
* ctrl+a  
    光标调到第一位
* ctrl+l   
    清屏
*  按住esc不放  再按两次 ZZ  
    退出vim模式
*  HEAD  
    指向的是当前分支，相当于一个指针