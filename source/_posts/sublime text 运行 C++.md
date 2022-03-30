---
index_img: https://img.xjh.me/random_img.php?type=bg&ctype=nature&return=302&seed=482172
title: sublime text 运行 C++
date: 2022-02-13 18:16:00
updated: 2022-02-13 18:16:00
tags:
  - 教程
categories:
  - 其他
comments: true
---
1. 首先需要[下载sublime text](https://www.sublimetext.com/)

2. 然后需要下载[MinGW](http://www.mingw.org/),DevC++自带可以不用下载

3. 然后需要配置到环境变量中，计算机(右键)-属性-高级系统设置-环境变量-Path-新建，再把刚才MinGW中的bin地址放进去。

4. 配置环境变量后，需要在sublime中打开Tools-Build System-New Build System，把一下代码放进去，随便取个名字保存在默认的路径中。

```
{
"cmd": ["g++","-Wall", "${file}", "-o", "${file_path}/${file_base_name}"],
"file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
"working_dir": "${file_path}",
"selector": "source.c, source.c++",
"encoding":"cp936",
"variants":
[
{
"name": "Run",
"cmd": ["cmd", "/c", "g++", "-Wall","${file}", "-o", "${file_path}/${file_base_name}", "&&", "cmd", "/c", "${file_path}/${file_base_name}"]
},
{
"name": "RunInCommand",
"cmd": ["cmd", "/c", "g++", "-Wall","${file}", "-o", "${file_path}/${file_base_name}", "&&", "start", "cmd", "/c", "${file_path}/${file_base_name} & echo.&pause"]
}
]
}
```

5. 然后需要设置运行快捷键，把以下代码放在Perferences-Key Bindings右侧窗口中，保存后重新打开sublime，就可以运行了

```
[
{"keys": ["f9"], "command": "build", "args": {"variant": "RunInCommand"}}
] 
```