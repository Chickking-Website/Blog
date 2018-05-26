---
title: 自动打素数表机
date: 2018-04-06 18:42:09
tags: [OI]
toc: true
---
预先打出部分素数表，对于数据范围不是非常大的题目，优化的效果比较明显。
```
#include <cstdio>
#include <cmath>

int getNumber(char const arg[]) {
	int length = 0, jw = 1;
	int ans;
	for (length = 0; arg[length] != '\0'; length++);
	for (int i = length - 1; i >= 0; i--) {
		ans += jw * (arg[i] - '0');
		jw *= 10;
	}
	return ans;
}

int l, r, cnt;
bool firstNum = true, putEndline = false;
int main(int argc, char const *argv[]) {
	if (argc == 1) {
		fprintf(stderr, "ERROR: Please input start and end number!\n");
		return 0;
	}
	l = getNumber(argv[1]);
	r = getNumber(argv[2]);
	printf("const int prime[] = {");
	if (putEndline)
		putchar('\n');
	for (int i = l; i <= r - 1; i++) {
		if (isPrime(i)) {
			if (!firstNum) {
				printf(", ");
				if (putEndline)
					putchar('\n');
			}
			printf("%d", i);
			cnt++;
			firstNum = false;
		}
	}
	if (putEndline)
		putchar('\n');
	printf("};\n");
	fprintf(stderr, "Count: %d\n", cnt);
	return 0;
}
```