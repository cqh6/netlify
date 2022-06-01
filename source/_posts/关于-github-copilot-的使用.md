---
title: 关于 GitHub Copilot 的使用
date: 2022-05-31 19:25:25
updated: 2022-06-01 10:27:39
categories:
  - 其他
comments: true
---
## GitHub Copilo 是什么

先推荐一个好康的漫画：[BetaCat](https://code2048.com/series/betacat)

![](/images/uploads/gitHubcopilot-6.png)

感觉这是一个很天才的想法，和 GitHub Copilot 有着异曲同工之妙！

GitHub Copilot 会根据你的上下文、注释、本地代码和 github 等上开源代码自动写出代码。

正确率还比较高吧！对于贺代码或许是一个不错的选择。

## 如何下载

[申请界面](https://copilot.github.com/)

点击以上链接进去后点击 `sign up`，用 github 登录后即可申请。

申请后等个几个月的样子，如果申请成功了，它会发一个邮件给你，然后就可以使用了。

可以参考[官网文档](https://github.com/github/copilot-docs)，这里介绍它在 vs code 上的使用。

首先下载 [vs code](https://code.visualstudio.com/)，在插件中搜索 `GitHub Copilot` 并安装。

![](/images/uploads/githubcopilot-1.png)

先新建一个代码（以下以 C++ 代码为例）

需要输入一些前置代码，比如注释、函数名等，它就可以帮你智能生成代码。

这时右下角会有一个图标，如果在转，说明它在思考，如果不转了，说明它不会写或者写完了qwq。

当它写完时，按下 `Tab` 可以使用它写下的代码，按下 `Ctrl+Enter` 可以选择其他代码，否则的话就会忽略它的辛辛苦苦写出来的代码。

![](/images/uploads/githubcopilot-7.png)

## 基本用法

1.它可以根据你的函数或封装名自动生成

![](/images/uploads/githubcopilot-2.png)

![](/images/uploads/githubcopilot-3.png)

2.也可以根据注释（最好是英文）生成

![](/images/uploads/githubcopilot-4.png)

3.还有从 github 上贺的qwq（图片来自网络，侵删）

![](/images/uploads/githubcopilot-5.gif)

4.当然，如果本地的代码，前提是你本地有代码。

~~可以利用这一点来贺题，就是先把题解代码搞到本地，然后你只要写一点点代码，它会根据你的码风和风格贺完这道题~~

5.还有各种功能等着大家发现

## 特点

1. 它会根据你的码风写代码，比如大括号是否单独占一行、符号两边是否要加空格等。
2. 它会根据你的风格写代码，比如你的数组下标是否从 0 开始。
3. 它甚至会根据你的取名风格取变量名，比如它检测到你的变量名喜欢用 `left` ， `right`，如果它在另一个地方贺到了 `u` 或 `d` ，它也会大概率给你改成 `up` 和 `down` 了。
4. 还有好多好多很厉害的地方。

## 后记

AI 写~~贺~~代码虽好，但不要忘记自己思考，它可以用来帮我们完成一些基础的实现，但真正的理解还是得考自己！