---
layout: post
title:  "show PR diff in terminal"
date:   2018-11-26 11:10:22 +0800
categories: develop
---

## 本地查看 Pull Request 改动

commits 对应命令：

`git log master..develop` 或者 `git log develop ^master`

changes 对应：

`git diff master...develop`

[ ![gitdiff&log](https://i.stack.imgur.com/4wMJI.png) ](https://stackoverflow.com/questions/7251477/what-are-the-differences-between-double-dot-and-triple-dot-in-git-dif/46345364#46345364)
