---
title: 关于 GitHub Copilot 的使用
date: 2022-05-31 19:25:25
updated: 2022-05-31 19:25:25
categories:
  - 其他
comments: true
---
## What is GitHub Copilot?

推荐一个漫画：[BetaCat](https://code2048.com/series/betacat)

![](/images/uploads/gitHubcopilot-6.png)

感觉这是一个很天才的想法，就像 GitHub Copilot 一样！

GitHub Copilot 会根据你的上下文、注释、本地代码和 github 等上开源代码自动写出代码，目前我测试用的正确率好像还挺高的qwq。

## How to  it?

[申请界面](https://copilot.github.com/)

点击以上链接申请后等一段时间，差不多几个月的样子，然后下载 [vs code](https://code.visualstudio.com/)，在插件中搜索 `GitHub Copilot` 并安装。

![](/images/uploads/githubcopilot-1.png)

然后就可以用了。

先新建一个代码（以下以 C++ 代码为例）

输入一些东西，比如函数开头、或者一句话写到一半，右下角的图标如果在转，说明它在思考，如果不转了，说明它不会写或者写完了qwq，当它写完时，按下 `Tab` 可以使用它写下的代码，按下 `Ctrl+Enter` 可以选择其他代码，按其他键可以忽略它的辛辛苦苦写的代码。

![](/images/uploads/githubcopilot-7.png)

它可以根据你的函数或封装名自动生成

![](/images/uploads/githubcopilot-2.png)

![](/images/uploads/githubcopilot-3.png)

也可以根据注释（最好是英文）生成

![](/images/uploads/githubcopilot-4.png)

还有从 github 上贺的qwq（图片来自网络，侵删）

![](/images/uploads/githubcopilot-5.gif)

当然，如果本地的代码，前提是你本地有代码。

还有它会根据你的码风写出代码，比如你大括号不喜欢单独占一行，就是它贺的代码的大括号是单独一行的，它也会给你改成不单独占一行的。再比如你的数组时下表从 $0$ 开始的，即使它贺的代码是从 $1$ 开始也会帮你改正。

## Postscript

AI 写~~贺~~代码虽好，但不要忘记自己思考，它是用来帮我们完成一些基础的实现，但真正的理解还是得考自己。

~~话说用这玩意贺代码是真的方便，把题解代码放到本地，你还没开始写它就已经根据你的码风写完了~~