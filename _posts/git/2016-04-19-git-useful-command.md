---
layout: post
title: "git常用命令"
date: 2016-04-19 10:04:00 +0800
categories: git
---
### 删除本地存在而远程仓库已经删除的分支
`git remote prune origin`

```
prune
Deletes all stale remote-tracking branches under <name>.
These stale branches have already been removed from the
remote repository referenced by <name>, but are still
locally available in "remotes/<name>".

With --dry-run option, report what branches will be pruned,
but do not actually prune them.
```
