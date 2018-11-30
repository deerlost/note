---
title: 常用的Git命令
tags:  Git
grammar_cjkRuby: true
---
**wyoung**

## 前言 ##

 1. git ls-files --stage  &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; --查看保存在stage的文件 
 2. git rm -r --cached .idea  #--cached不会把本地的.idea删除
git commit -m 'delete .idea dir'
git push -u origin master 	
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--在 GitHub.com 上删除某个 Repository 中的某个文件夹
 ### git diff ### 
 
 1. git diff < branch1 >  < branch > &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--将自己的分支和其他分支进行对比
 2 git diff --cached&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--对比暂存区和当前的HEAD所指向的branch
 ### 恢复暂存区 ###
 
 1. git rm path/to/file --cached &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--将文件从暂存区索引中删除
 2. git rm file -f &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--完全删除文件
 ### git reset
 
 1. git reset --hard &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--去除所有修改,包括索引中的内容和工作目录中的修改
 2. git reset --mixed &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--重置索引
 3. git reset --soft&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; --修改分支的HEAD
 4. git reset HEAD &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--重置HEAD
### git stash
 可以通过git stash pop 命令将之前的改动恢复过来，但是如果工作目录中有未追踪的文件，默认情况下不会将其存入临时储藏区。

 1. git stash --include-untracked &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--临时保存未追踪的文件
 2. git stash list &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--列出临时储藏区中的内容

 ### 历史记录
 git log 命令可以显示HEAD、所有提交的ID以及分支信息
 
 1. git show COMMIT-ID/HEAD/BRANCH &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--显示上述信息
 2. git diff HEAD HEAD-2 &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--对比当前HEAD和前两个提交
 3. git log --patch &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--展示每个提交中更详细的信息

### 补丁操作(生产patch和打入patch)

git format-patch生成的补丁文件默认从1开始顺序编号，并使用对应提交信息中的第一行作为文件名。如果使用了-- numbered-files选项，则文件名只有编号，不包含提交信息；如果指定了--stdout选项，可指定输出位置，如当所有patch输出到一个文件；可指定-o <dir>指定patch的存放目录；

 1. git format-patch -M master
 2. git format-patch 4e16  &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--某次提交以后的所有patch，4e16指的是commit名
 3. git format-patch --root 4e16 &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--从根到指定提交的所有patch:
 4. git format-patch 365a..4e16 &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--某两次提交之间的所有patch--365a和4e16分别对应两次提交的名称
 5. git format-patch –n 07fe&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--某次提交（含）之前的几次提交，--n指patch数，07fe对应提交的名称
故，单次提交即为：
git format-patch -1 07fe

 



 