---
title: 常用的Git命令
tags: 
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
 
 3. git diff < branch1 >  < branch > &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--将自己的分支和其他分支进行对比
 4. git diff --cached&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--对比暂存区和当前的HEAD
 ### 恢复暂存区 ###
 
 5. git rm path/to/file --cached &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--将文件从暂存区索引中删除
 6. git rm file -f &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--完全删除文件
 ### git reset
 
 7. git reset --hard &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--去除所有修改,包括索引中的内容和工作目录中的修改
 8. git reset --mixed &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--重置索引
 9. git reset --soft&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; --修改分支的HEAD
### git stash
 可以通过git stash pop 命令将之前的改动恢复过来，但是如果工作目录中有未追踪的文件，默认情况下不会将其存入临时储藏区。

 10. git stash --include-untracked &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--临时保存未追踪的文件
 11. git stash list &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--列出临时储藏区中的内容
 ### 历史记录
 git log 命令可以显示HEAD、所有提交的ID以及分支信息
 
 12. git show COMMIT-ID/HEAD/BRANCH &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--显示上述信息
 13. git diff HEAD HEAD-2 &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--对比当前HEAD和前两个提交
 14. git log --patch &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;--展示每个提交中更详细的信息
 