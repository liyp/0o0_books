# Git

## version control flow

* [a-successful-git-branching-model](http://www.oschina.net/translate/a-successful-git-branching-model)

some branches like: master、develop、release-\*、hotfix-\*

&gt; $ git checkout develop Switched to branch 'develop'  
**$ git merge --no-ff myfeature**  
Updating ea1b82a..05e9557  
\(Summary of changes\)  
$ git branch -d myfeature  
Deleted branch myfeature \(was 05e9557\).  
$ git push origin develop

`git merge --no-ff xx` 标志导致合并操作创建一个新commit对象，即使该合并操作可以fast-forward。这避免了丢失这个功能分支存在的历史信息，将该功能的所有提交组合在一起。

> [git-merg](https://github.com/liyp/0o0_books/tree/a0c14fcca258dce040adae2c489930ca71dea410/daybyday/git-scm.com/docs/git-merge/README.md)

## reflog

## push

免去多次输入密码，缓存凭证： credential cache `git config --global credential.helper cache`

```text
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
ssh-add ~/.ssh/id_rsa
xclip -sel clip < ~/.ssh/id_rsa.pub
ssh -T git@github.com
```

> credential store `git config --global credential.helper store`  
> \[credential\]\)\([https://git-scm.com/docs/gitcredentials](https://git-scm.com/docs/gitcredentials)\) 这个更厉害

## other

[Mercurial](https://mercurial.selenic.com)（轻量级分布式版本控制系统）

