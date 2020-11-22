---
title: GIT_ADD和COMMIT时都做了什么
abbrlink: 61078
date: 2020-04-28 20:48:05
categories: git
tags:
  - git
  - git add
  - git commit
---

探究 add 和 commit 之前，需要先明白GIT的几个基础概念

```bash
                                                                                                                                             git fetch\pull
                      git restore --staged files                                                                                                   lone
  +-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-+                                                  +.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.+
  v                                                   !                                                  v                                                       !
+-------------------+                               +-------------+                                    +-----------------------------------+                   +-------------------+
| Working Directory |  git add files                | Staged Area |  git commit                        | .git directory\Repository\History |  git push         | Remote Repository |
|     Modified      | ============================> |    Index    | =================================> |             Committed             | ================> |                   |
+-------------------+                               +-------------+                                    +-----------------------------------+                   +-------------------+
                                                      ^             git restore --source HEAD files      !
                                                      +-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.-.+

```


工作区 (working Directory)：
索引_暂存区 (Stage\index) ：
版本库_历史记录 (History)  ：

https://www.ibm.com/developerworks/cn/opensource/os-cn-git-and-github-3/index.html
https://marklodato.github.io/visual-git-guide/index-zh-cn.html
https://www.dazhuanlan.com/2020/01/02/5e0d45e263ada/
https://morningspace.github.io/tech/inside-git-2/
https://juejin.im/post/5d27dfb7f265da1b827ac789
https://juejin.im/post/5c2d7377518825544d43dfa5#heading-35
https://juejin.im/post/5dbbc522f265da4d24590408#heading-4
https://juejin.im/post/5afc5242f265da0b7f44bee4
