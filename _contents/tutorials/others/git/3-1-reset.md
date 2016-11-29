---
youku_id: 
youtube_id: 
description: 
chapter: 3
title: 回到从前 (reset)
comments: true
thumbnail: /static/thumbnail/git/3-01.jpg
---
* 学习资料:
  * [这节例子的初始文件](https://github.com/MorvanZhou/tutorials/blob/master/gitTUT/for_gitTUT_3-1.zip)
  
本节内容分为:
  
* [修改已 commit 的版本](#amend)
* [回到过去 reset](#reset)
  * [回到 add 之前](#undo-add)
  * [回到 commit 之前](#undo-commit)


<h4 class="tut-h4-pad" id="amend">修改已 commit 的版本</h4>

有时候我们总会忘了什么, 比如已经提交了 `commit` 却发现在这个 `commit` 中忘了附上另一个文件.
接下来我们模拟这种情况. 上节内容中, 我们最后一个 `commit` 是 `change 2`, 我们将要添加另外一个文件, 
将这个修改也 `commit` 进 `change 2`. 所以我们复制 `1.py` 这个文件, 改名为 `2.py`. 
并把 `2.py` 变成 `staged`, 然后使用 `--amend` 将这次改变合并到之前的 `change 2` 中.

```shell
$ git add 2.py
$ git commit --amend --no-edit   # "--no-edit": 不编辑, 直接合并到上一个 commit
$ git log --oneline    # "--oneline": 每个 commit 内容显示在一行

# 输出
904e1ba change 2    # 合并过的 change 2
c6762a1 change 1
13be9a7 create 1.py
```

<h4 class="tut-h4-pad" id="reset">回到过去 reset</h4>

<h5 id="undo-add">回到 `add` 之前</h5>

有时我们添加 `add` 了修改, 但是又后悔, 并想补充一些内容再 `add`. 这时,
我们有一种方式可以回到 `add` 之前. 比如在 `1.py` 文件中添加这一行:

```python
d = 3
```

然后 `add` 去 `staged` 再返回到 `add` 之前:
 
```shell
$ git add 1.py
$ git status -s # "-s": status 的缩写模式
# 输出
M  1.py     # staged
-----------------------
$ git reset 1.py
# 输出
Unstaged changes after reset:
M	1.py
-----------------------
$ git status -s
# 输出
 M 1.py     # unstaged
```

<h5 id="undo-commit">回到 `commit` 之前</h5>

在穿梭到过去的 `commit` 之前, 我们必须了解 git 是如何一步一步累加更改的.
我们截取网上的一些图片 [http://bramus.github.io/ws2-sws-course-materials/xx.git.html](http://bramus.github.io/ws2-sws-course-materials/xx.git.html)

<img class="course-image" src="/static/results/git/2-2-1.png">
<img class="course-image" src="/static/results/git/2-2-2.png">
<img class="course-image" src="/static/results/git/2-2-3.png">
<img class="course-image" src="/static/results/git/2-2-4.png">

每个 `commit` 都有自己的 `id` 数字号, `HEAD` 是一个指针, 
指引当前的状态是在哪个 `commit`. 最近的一次 `commit` 在最右边, 我们如果要回到过去,
就是让 `HEAD` 回到过去并 `reset` 此时的 `HEAD` 到过去的位置.

**注意: 以下步骤一旦执行, 而且关闭了 terminal 窗口, 就没有回头路了**

```shell
# 之前做了一些 add 工作, 这一步让我们回到 add 前
$ git reset --hard HEAD    
# 输出
HEAD is now at 904e1ba change 2
-----------------------
# 看看所有的log
$ git log --oneline
# 输出
904e1ba change 2
c6762a1 change 1
13be9a7 create 1.py
-----------------------
# 回到 c10ea64 change 1
# 方式1: "HEAD^"
$ git reset --hard HEAD^  

# 方式2: "commit id"
$ git reset --hard c6762a1
-----------------------
# 看看现在的 log
$ git log --oneline
# 输出
c6762a1 change 1
13be9a7 create 1.py
```

怎么 `change 2` 消失了!!! 还有办法挽救消失的 `change 2` 吗? 只要你的 terminal 窗口没有关闭, 那还是有救的.
我们可以查看 `$ git reflog` 里面最近做的所有修改, 并选择想要挽救的 `commit id`:

```shell
$ git reflog
# 输出
c6762a1 HEAD@{0}: reset: moving to c6762a1
904e1ba HEAD@{1}: commit (amend): change 2
0107760 HEAD@{2}: commit: change 2
c6762a1 HEAD@{3}: commit: change 1
13be9a7 HEAD@{4}: commit (initial): create 1.py
```

重复 `reset` 步骤就能回到 `commit (amend): change 2` (id=904e1ba)这一步了:

```shell
$ git reset --hard 904e1ba
$ git log --oneline
# 输出
904e1ba change 2
c6762a1 change 1
13be9a7 create 1.py
```

我们又再次奇迹般的回到了 `change 2`. 在学这一节内容的时候要小心..不然没有回头路了.

