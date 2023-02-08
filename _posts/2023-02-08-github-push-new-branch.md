---
title: 在Github已有的仓库下push一个新建的分支
date: 2023-02-28 09:43:00 +0800
categories: [Git]
tags: [Backend]
render_with_liquid: false
---

每次都忘，记一下。

# 1. 把远程仓库拉下来

- `git clone <github URL>`：会在当前目录下产生一个新的文件夹，名字为项目名称

- `cd <上面那个文件夹的名字>`

- 查看本地和远程的所有分支

  ```bash
  (base) xbj@naobaijin xxxx % git branch -al
  * main
    remotes/origin/HEAD -> origin/main
    remotes/origin/LJD
    remotes/origin/augu_data
    remotes/origin/augu_data_3channel
    remotes/origin/ljd
    remotes/origin/main
    remotes/origin/queenie_0522
    remotes/origin/split_pt_xbj
    remotes/origin/xbj
  ```

  其中master是本地分支，星号`*`表示正在使用的分支
  带有`remotes`的分支都是远程分支

- `git status`查看当前状态

  ```bash
  Your branch is up to date with 'origin/main'.
  nothing to commit, working tree clean
  ```

  当前工作环境clean，而且没有需要commit的

# 2. 新建一个本地分支

- `git checkout -b <branch name>`新建分支并挪动到新分支上

  ```bash
  git checkout -b STI_xbj 
  Switched to a new branch 'STI_xbj'
  ```

- 查看当前分支状态

  ```bash
  git branch
  * STI_xbj
    main
  ```



# 3. 改仓库代码

- 该删的删，该加的加
- 然后`status add commit`三步走，直到`nothing to commit, working tree clean`



# 4. Push当前分支到远程分支

- git push origin STI_xbj:STI_xbj

```bash
git push origin STI_xbj:STI_xbj

Enumerating objects: 19, done.
Counting objects: 100% (19/19), done.
Delta compression using up to 8 threads
Compressing objects: 100% (17/17), done.
Writing objects: 100% (17/17), 31.34 KiB | 6.27 MiB/s, done.
Total 17 (delta 1), reused 2 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
remote: 
remote: Create a pull request for 'STI_xbj' on GitHub by visiting:
remote:      https://github.com/xxxx/yyyyy/pull/new/STI_xbj
remote: 
To https://github.com/xxxx/yyyyy.git
 * [new branch]      STI_xbj -> STI_xbj

```

- 然后就能看到

<img src="https://user-images.githubusercontent.com/84035000/217409538-62637885-837b-4e33-9b3b-9311691e491e.png" alt="image" style="zoom:67%;" />