---
title: "Git Push Permission Error"
date: 2018-01-08T18:53:38+08:00
draft: false
---

mac上同时使用两个github帐号`git clone`，结果`git push`时遭遇push权限错误

```bash
mac:public $ git push origin master
remote: Permission to cheg/cheg.github.io.git denied to chex.
fatal: unable to access 'https://github.com/cheg/cheg.github.io.git/': The requested URL returned error: 403
```

解决办法:重新设置`remote.origin.url`值

```bash
git remote set-url origin https://cheg@github.com/cheg/cheg.github.io.git
```

使用`git remote set-url`前后对比:

```bash
mac:public $ git config --list
...
remote.origin.url=https://github.com/cheg/cheg.github.io.git        # 设置前
...

mac:public $ git remote set-url origin https://cheg@github.com/cheg/cheg.github.io.git

mac:public $ git config --list
...
remote.origin.url=https://cheg@github.com/cheg/cheg.github.io.git   # 设置后
...

mac:public $ git push origin master     # 可以顺利push了:)
Counting objects: 35, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (27/27), done.
Writing objects: 100% (35/35), 8.29 KiB | 2.76 MiB/s, done.
Total 35 (delta 15), reused 0 (delta 0)
remote: Resolving deltas: 100% (15/15), completed with 8 local objects.
To https://github.com/cheg/cheg.github.io.git
   11d939c..e542815  master -> master
```
