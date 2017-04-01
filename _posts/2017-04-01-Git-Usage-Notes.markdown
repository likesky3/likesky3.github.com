---
layout:post
title: "Git Usage Notes"
date: 2017-04-01
category: Coding Tools
tags: Git
---

#### Case1
不小心git add & commit了一个超过git限制的大文件, 出现错误描述如下:
>remote: error: File lib/openjdk-8-src-b132-03_mar_2014.zip is 118.35 MB; this exceeds GitHub's file size limit of 100.00 MB

**Solution**
**git filter-branch** -f --index-filter 'git rm --cached --ignore-unmatch lib/openjdk-8-src-b132-03_mar_2014.zip'



