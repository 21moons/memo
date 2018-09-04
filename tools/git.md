* Git 简易指南
http://www.bootcss.com/p/git-guide/

* Git 合并多个 Commit
https://www.jianshu.com/p/964de879904a

* Git 获取远程分支
1. 先运行 git fetch, 将远程分支代码获取到本地.
2. 再运行 git checkout -b local-branchname origin/remote_branchname  就可以将远程分支映射到本地命名为 local-branchname 的分支.

* Git 比较两个分支的差异 
Git diff branch1 branch2 --stat              //显示出所有有差异的文件列表
Git diff branch1 branch2 文件名(带路径)       //显示指定文件的详细差异
Git diff branch1 branch2                     //显示出所有有差异的文件的详细差异

* Git 查看某个文件的修改
git log "文件路径\文件名"

* Git 显示每个更新之间的差异
git log -p

* 通过提交说明中过滤出相关提交
git log --grep "xxx"
git log --grep "condition 1" --grep "condition 2" --all-match  // 多个条件

* Git 查看指定提交下某个文件的修改
git diff "CommitID" "文件路径\文件名"