---
title: 数组初始化长度问题
date: 2017-12-21 13:33:20
tags: [OI,C++]
toc: true
---
最近正在 studying OI，今天中午闲的没事写了个冒泡排序。

## 题目
### 题目描述
输入整数进行升序排序。
### 输入格式
共两行，第一行输入数字 $n$，第二行输入 $n$ 个数字。
### 输出格式
输出排序完成的数据。
### 样例
#### 输入样例
```
6
3 4 8 5 7 9
```
#### 输出样例
```
3 4 5 7 8 9
```
### 数据范围与提示
对于 $100\%$ 的数据，都属于区间 $(0,1000)$。

### AC 代码:
```C++
#include <iostream>
using std::cin;
using std::cout;
using std::endl;

int main() {
    int N = 0;
    cin >> N;
    int a[N];
    for (int i = 0; i < N; i++) cin >> a[i];
    int tmpVar = 0;
    for (int i = 0; i < N; i++)
        for (int j = N - 1; j >= 0; j--) {
            if (a[j - 1] > a[j]) {
                tmpVar = a[j];
                a[j] = a[j - 1];
                a[j - 1] = tmpVar;
            }
    }
    for (int i = 0; i < N; i++) {
        cout << a[i] << " "
    }
    cout << endl;
    return 0;
}
```

## 疑惑
很多书本上看到，数组的长度不能使用变量定义。但是目前来看，用变量定义数组长度的代码在 gcc、clang 下都可以直接编译通过，且没有 Warning。不知道这种做法在考场上会不会有风险。