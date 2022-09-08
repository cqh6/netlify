---
title: Markdown 制作课件教程
date: 2022-09-03 14:46:18
updated: 2022-09-03 14:46:18
categories:
  - 其他
comments: true
---
~~好NT的想法~~

首先在**新版** Typora 的 [偏好设置]-[导出]-[PDF] 中把导出大小设置成 $200\times112.5$，上下边距改成 $6$，左右边距改成 $12$，比较适合课件的大小。

再把写好的 md 文件运行以下代码即可生成一个新的 md 文件

再把新的 md 导出成 PDF 就好了

再推荐一个漂亮的 Typora 主题，很适合做课件的：[Seniva](https://theme.typoraio.cn/theme/Seniva/)

```cpp
#include <bits/stdc++.h>

using namespace std;

string s, st, title;
int tot=0;

void print() {
	puts(st.c_str());
	puts("<div STYLE=\"page-break-after: always;\"></div>\n");
}

int main() {
	freopen("in.md", "r", stdin);
	freopen("out.md", "w", stdout);
	while (getline(cin, s)) {
		nxt:;
		if (s[0]=='#') st=s+"\n", title=s, tot=0;
		else {
			if (s[0]=='>') {
				while ((s[0]=='>' || !s.size())) st+=s+"\n", getline(cin, s);
				print(); st=title.size()?(title+"\n"):"", tot=0;
				goto nxt;
			}
			if (s.size()>2 && s[0]=='`' && s[1]=='`' && s[2]=='`') {
				st=s+"\n", getline(cin, s);
				while (!(s.size()>2 && s[0]=='`' && s[1]=='`' && s[2]=='`')) st+=s+"\n", getline(cin, s);
				st+=s+"\n";
				print(), st=title.size()?(title+"\n"):"", tot=0;
				continue;
			}
			if (s.size()>3 && (s[0]=='!' && s[1]=='[' && s[2]==']' && s[3]=='(' || s[0]=='<' && s[1]=='i' && s[2]=='m' && s[3]=='g')) {
				if (tot>=3) {
					print();
					if (title.size()) st=title+"\n";
					else st="";
					tot=4;
					continue;
				}
				else tot+=3;
			}
			st+=s+"\n";
			if (s.size()) {
				tot++;
				if (tot==6) {
					print();
					if (title.size()) st=title+"\n";
					else st="";
					tot=0;
					continue;
				}
			}
		}
		if (s.size()) print();
	}
	return 0;
}
```

已实现功能（可自己小改）：

- 每次只输出一行
- 遇到标题自动换页
- 代码块一起输出
- 引用块一起输出
- 行数多（$\ge 6$）自动换新页（并一直保留标题）
- 代码后换新页
- 引用块后换新页
- 钦定一个图片占 4 行，防止占太多

如果你不想让引用段自动换页，可以改成下面这样：

```cpp
#include <bits/stdc++.h>

using namespace std;

string s, st, title;
int tot=0;

void print() {
	puts(st.c_str());
	puts("<div STYLE=\"page-break-after: always;\"></div>\n");
}

int main() {
	freopen("in.md", "r", stdin);
	freopen("out.md", "w", stdout);
	while (getline(cin, s)) {
		nxt:;
		if (s[0]=='#') st=s+"\n", title=s, tot=0;
		else {
			if (s[0]=='>') {
				while ((s[0]=='>' || !s.size())) st+=s+"\n", getline(cin, s), tot+=s.size()>2;
				print();
				if (tot>=6) {
					st=title.size()?(title+"\n"):"";
					tot=0;
					goto nxt;
				}
			}
			if (s.size()>2 && s[0]=='`' && s[1]=='`' && s[2]=='`') {
				st=s+"\n", getline(cin, s);
				while (!(s.size()>2 && s[0]=='`' && s[1]=='`' && s[2]=='`')) st+=s+"\n", getline(cin, s);
				st+=s+"\n";
				print(), st=title.size()?(title+"\n"):"", tot=0;
				continue;
			}
			if (s.size()>3 && (s[0]=='!' && s[1]=='[' && s[2]==']' && s[3]=='(' || s[0]=='<' && s[1]=='i' && s[2]=='m' && s[3]=='g')) {
				if (tot>=3) {
					print();
					if (title.size()) st=title+"\n";
					else st="";
					tot=4;
					continue;
				}
				else tot+=3;
			}
			st+=s+"\n";
			if (s.size()) {
				tot++;
				if (tot>=6) {
					print();
					if (title.size()) st=title+"\n";
					else st="";
					tot=0;
					continue;
				}
			}
		}
		if (s.size()) print();
	}
	return 0;
}
```