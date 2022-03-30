---
index_img: https://img.xjh.me/random_img.php?type=bg&ctype=nature&return=302&seed=958120
title: C++ 矩阵乘法不同枚举顺序速度测试
date: 2021-11-19 09:29:00
updated: 2021-11-19 09:29:00
tags:
  - 矩阵
categories:
  - 算法
comments: true
---
注：所以时间均在[Judge Duck Online](https://duck.ac/problem/mmmd1k)测试，用一维数组存储，其实二维数组储存也是一样的，因为地址都是按行连续的，按测试时间排序。

```cpp
//ikj:707.201 ms
#define idx(i, j) ((i) * n + (j))

void matrix_multiply(int n, const double *A, const double *B, double *C) {
	for (int i = 0; i < n; i++)
		for (int k = 0; k < n; k++)
			for (int j = 0; j < n; j++)
				C[idx(i, j)] += A[idx(i, k)] * B[idx(k, j)];
}
```

```cpp
//kij:860.137 ms
#define idx(i, j) ((i) * n + (j))

void matrix_multiply(int n, const double *A, const double *B, double *C) {
	for (int k = 0; k < n; k++)
		for (int i = 0; i < n; i++)
			for (int j = 0; j < n; j++)
				C[idx(i, j)] += A[idx(i, k)] * B[idx(k, j)];
}
```

```cpp
//ijk:6.212 s
#define idx(i, j) ((i) * n + (j))

void matrix_multiply(int n, const double *A, const double *B, double *C) {
	for (int i = 0; i < n; i++)
		for (int j = 0; j < n; j++)
			for (int k = 0; k < n; k++)
				C[idx(i, j)] += A[idx(i, k)] * B[idx(k, j)];
}
```

```cpp
//jik:8.07 s
#define idx(i, j) ((i) * n + (j))

void matrix_multiply(int n, const double *A, const double *B, double *C) {
	for (int j = 0; j < n; j++)
		for (int i = 0; i < n; i++)
			for (int k = 0; k < n; k++)
				C[idx(i, j)] += A[idx(i, k)] * B[idx(k, j)];
}
```

```cpp
//jki:>10 s
#define idx(i, j) ((i) * n + (j))

void matrix_multiply(int n, const double *A, const double *B, double *C) {
	for (int j = 0; j < n; j++)
		for (int k = 0; k < n; k++)
			for (int i = 0; i < n; i++)
				C[idx(i, j)] += A[idx(i, k)] * B[idx(k, j)];
}
```

```cpp
//kji:>10 s
#define idx(i, j) ((i) * n + (j))

void matrix_multiply(int n, const double *A, const double *B, double *C) {
	for (int k = 0; k < n; k++)
		for (int j = 0; j < n; j++)
			for (int i = 0; i < n; i++)
				C[idx(i, j)] += A[idx(i, k)] * B[idx(k, j)];
}
```

发现第三重循环如果需要用的数在同一行中速度可以达到最快，ikj 和 kij 都是较快的方法

## 参考
- [C++加速矩阵乘法的最简单方法 - 知乎](https://zhuanlan.zhihu.com/p/146250334)