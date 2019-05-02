# bashrc

这里主要是对git的修改可以显示当前的分支和目录过长后省略,修改完成后` source .bashrc`

vim ~/.bashrc

```shell
# ll 别名修改
alias ll='ls -lah'
# 配置git显示分支名称问题
 function git_branch {
   branch="`git branch 2>/dev/null | grep "^\*" | sed -e "s/^\*\ //"`"
   if [ "${branch}" != "" ];then
       if [ "${branch}" = "(no branch)" ];then
           branch="(`git rev-parse --short HEAD`...)"
       fi
       echo " ($branch)"
   fi
 }
 export PS1='\u@\h \[\033[01;36m\]\W\[\033[01;32m\]$(git_branch)\[\033[00m\] \$ '
```

