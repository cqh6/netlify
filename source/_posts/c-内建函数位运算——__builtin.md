---
title: C++内建函数位运算——__builtin
date: 2022-05-23 10:51:11
updated: 2022-05-23 10:51:11
tags: []
categories:
  - 其他
comments: true
---
C++ 中有些关于位运算的牛马函数，通过查表实现，复杂度好像是 $O(1)$ 的吧，反正特别快！

~~虽然大部分都没什么用~~

**①** `int __builtin_popcount(unsigned int n)` 返回 $n$ 二进制中 $1$ 的个数

```cpp
printf("%d\n", __builtin_popcount(13));
//13(1101)->3
```

**②** `int __builtin_parity(unsigned int n)`  返回 $n$ 二进制中 $1$ 的个数的奇偶性（好像没屁用，就相当于 `__builtin_popcount(n)&1`）

```cpp
printf("%d %d\n", __builtin_parity(12), __builtin_parity(13));
//12(1100)->0
//13(1101)->1
```

**③** `int __builtin_ffs(int n)`  返回 $n$ 二进制中最后一个 $1$ 的位置，若不存在返回 $0$，从低位到高位从 $1$ 开始编号

```cpp
printf("%d\n", __builtin_ffs(12));
//12(1100)->3
```

**④** `int __builtin_clrsb(unsigned int n)` 返回 $x$ 二进制中前导 $0$ 的个数，若 $x<0$，则返回 $x$ 前导 $1$ 的个数。~~什么牛马东西？~~

**⑤** `int __builtin_clz(unsigned int n)` 返回 $x$ 二进制中前导 $0$ 的个数（$x\ne 0$）

```cpp
printf("%d\n", __builtin_clz(12));
//12(00000000000000000000000000001100)->28
```

我们发现一个数以 $2$ 为底的对数即为这个数 $\tt二进制位数-1$，我们可以用这个函数快速求出对数下取整后的结果。

虽然 `<cmaht>` 中的 `log2(x)` 函数也是 $O(1)$ 的，但是常数特别大，相比之下 `clz` 快很多。

求一个 $32$ 位整型以 $2$ 为底取整后的对数：`31-__builtin_clz(n)`

求一个 $64$ 位整型以 $2$ 为底取整后的对数：`63-__builtin_clzll(n)`

**⑥** `int __builtin_ctz(unsigned int n)` 返回 $x$ 二进制中末尾连续 $0$ 的个数（$x\ne 0$）

```cpp
printf("%d\n", __builtin_ctz(12));
//12(1100)->2
```

---

以上都是大部分针对 $32$ 位无符号整型的，若需要更大的，只需在函数后加上 `l` 或 `ll` 即可。

`l` 范围为 `long` 的范围，由系统决定。

`ll` 范围为 `long long` 的范围，为 $64$ 位整型。

如计算 一个 $64$ 位整型的 `popcount` 即为 `__builtin_popcountll(unsigned long long n)`

如果更大的话可以用 `bitset` 来实现各种位运算qwq