---
layout: post
title:  "Use javac&java cmd to run a java program"
date:   2017-04-28 
categories:  Tools, Java 
---

很多Java初学者会碰到"Could not find or load main class xx"问题。
[这里有篇很好的解答](http://stackoverflow.com/questions/18093928/what-does-could-not-find-or-load-main-class-mean)

此处简要记录我一直没完全理解的PATH和CLASSPATH问题。
在PATH环境变量中增加JDK的bin目录即可在任意目录下运行javac, java等JDK自带的命令行工具.

java执行命令是
java [<option>...] <class-name> [<argument>]
此处class-name必须是fully qualified也就是说如果有包名，必须写全。
如果某个类有包名，则不能在它所在的文件夹里头执行，必须在包外目录执行。
比如文件组织形式是Learning/src/com/abc/utils/Password.java，我们不能跑进utils目录内运行java Password，我们应该在src目录或者Learning目录执行。
加入在Learning目录，如果我们没有配置classpath， 则应该是java -cp ./src com.abc.utils.Password. 如果我们的classpath配置中写上了./src，则执行中就可以省去"-cp ./src"
