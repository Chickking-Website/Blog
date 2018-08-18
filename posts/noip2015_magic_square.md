---
title: 「NOIP2015」神奇的幻方
date: 2017-12-30 8:31:20
tags: [OI,C++,NOIP]
toc: true
---
## 题目描述
幻方是一种很神奇的 $N \times N$ 矩阵：它由数字 $1,2,3,\cdots \cdots ,N \times N$ 构成，且每行、每列及两条对角线上的数字之和都相同。

当 $N$ 为奇数时，我们可以通过下方法构建一个幻方：

首先将 $1$ 写在第一行的中间。

之后，按如下方式从小到大依次填写每个数 $K (K=2,3,\cdots,N \times N)$ ：

1.若 $(K-1)$ 在第一行但不在最后一列，则将 $K$ 填在最后一行， $(K-1)$ 所在列的右一列；

2.若 $(K-1)$ 在最后一列但不在第一行，则将 $K$ 填在第一列， $(K-1)$ 所在行的上一行；

3.若 $(K-1)$ 在第一行最后一列，则将 $K$ 填在 $(K-1)$ 的正下方；

4.若 $(K-1)$ 既不在第一行，也不在最后一列，如果 $(K-1)$ 的右上方还未填数，则将 $K$ 填在 $(K-1)$ 的右上方，否则将 $K$ 填在 $(K-1)$ 的正下方。

现给定 $N$ ，请按上述方法构造 $N \times N$ 的幻方。

## 输入格式
输入文件只有一行，包含一个正整数 $N$ ，即幻方的大小。

## 输出格式
输出文件包含 $N$ 行 ，每行 $N$ 个整数，即按上述方法构造出的 $N \times N$ 的幻方，相邻两个整数之间用单空格隔开。

## 样例
### 输入样例
```plain
3
```
### 输出样例:
```plain
8 1 6
3 5 7
4 9 2
```
## 数据范围与提示:
对于全部的数据， $1 \leq N \leq 39$ 且 $N$ 为奇数。

时间限制：1 s

空间限制：128 MiB

## AC 代码
```c++
#include <iostream>
using std::cin;
using std::cout;
using std::endl;
const int MAX_ROW = 39 + 1, MAX_COLUMN = 39 + 1;
int sq[MAX_COLUMN][MAX_ROW], n, column, row;

int main() {
	cin >> n;
	row = 1;
	column = (n / 2) + 1;
	sq[column][row] = 1;
	for (int i = 2; i <= n * n; i++) {
		if (row == 1 && column != n) {
			row = n;
			column++;
		}
		else if (column == n && row != 1) {
			column = 1;
			row--;
		}
		else if (row == 1 && column == n) {
			row++;
		}
		else {
			if (sq[column + 1][row - 1] == 0) {
				row--;
				column++;
			}
			else {
				row++;
			}
		}
		sq[column][row] = i;
	}
	for (int i = 1; i <= n; i++) {
		for (int j = 1; j <= n; j++) {
			cout << sq[j][i] << " ";
		}
		cout << endl;
	}
} 

/*
#2. 「NOIP2015」神奇的幻方
https://ly.men.ci/problem/2
*/
```
