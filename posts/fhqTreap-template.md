---
title: 非旋转 Treap 模板
date: 2018-09-15 15:44:33
tags: [OI, 算法模板, 数据结构, 平衡树]
toc: true
---
题目传送门: [洛谷 P3369](https://www.luogu.org/problemnew/show/P3369)、[LibreOJ #104](https://loj.ac/problem/104)、[BZOJ 3224](https://www.lydsy.com/JudgeOnline/problem.php?id=3224)

我们需要实现一种数据结构，实现以下操作。
1. 插入 $ x $ 数；
2. 删除 $ x $ 数（若有多个相同的数，因只删除一个）；
3. 查询 $ x $ 数的排名（若有多个相同的数，因输出最小的排名）；
4. 查询排名为 $ x $ 的数；
5. 求 $ x $ 的前趋（前趋定义为小于 $ x $，且最大的数）；
6. 求 $ x $ 的后继（后继定义为大于 $ x $，且最小的数）。

很明显，可以通过 Splay 来做，但是我不会，因此我们可以使用非旋转 Treap 来做。

AC 代码:
```cpp
#include <cstdio>
const int MAXN = 200000 + 10;
namespace fhqTreap {
	const int INF = 0x7fffffff;
	inline int rand() {
		static unsigned long long seed = 19260817;
		return seed = (seed * 47821 + 0x1317D1E) % 0x1317CB3;
	}
	int root, count;
	struct Node {
		int l, r, k, rd, size;
		Node() {}
		Node(int k) : l(0), r(0), k(k), rd(rand()), size(1) {}
	} node[MAXN];
	void update(int const n) {
		node[n].size = (node[n].l ? node[node[n].l].size : 0) + (node[n].r ? node[node[n].r].size : 0) + 1;
	}
	void split(int const n, int const k, int &x, int &y) {
		if (!n) x = y = 0;
		else if (k <= node[n].k) {
			split(node[n].l, k, x, y);
			node[n].l = y;
			update(y = n);
		}
		else {
			split(node[n].r, k, x, y);
			node[n].r = x;
			update(x = n);
		}
	}
	int merge(int const x, int const y) {
		if (!x || !y) return x + y;
		if (node[x].rd < node[y].rd) {
			node[x].r = merge(node[x].r, y);
			return update(x), x;
		}
		else {
			node[y].l = merge(x, node[y].l); // X < NODE[Y].L!!!!!!!!!
			return update(y), y;
		}
	}
	inline void insert(int const &k) {
		int x, y;
		split(root, k, x, y);
		node[++count] = Node(k);
		root = merge(merge(x, count), y);

	}
	inline void remove(int const &k) {
		int x, y, z;
		split(root, k, x, z), split(z, k + 1, z, y);
		z = merge(node[z].l, node[z].r);
		root = merge(merge(x, z), y);
	}
	inline int rank(int const &k) {
		int x, y, ans;
		split(root, k, x, y);
		if (!x) return (root = merge(x, y)), 1;
		ans = node[x].size + 1;
		return (root = merge(x, y)), ans;
	}
	inline int search(int const rk, int n = root) {
		int rank = (node[n].l ? node[node[n].l].size : 0) + 1;
		if (rank == rk) return node[n].k;
		if (rk < rank) {
			return search(rk, node[n].l);
		}
		else {
			return search(rk - rank, node[n].r);
		}
	}
	inline int lower(int const k) {
		int x, y, t;
		split(root, k, x, y), t = x;
		if (!x) return -INF;
		while (node[t].r) t = node[t].r;
		return (root = merge(x, y)), node[t].k;
	}

	inline int upper(int const k) {
		int x, y, t;
		split(root, k + 1, x, y), t = y;
		if (!y) return INF;
		while(node[t].l) t = node[t].l;
		return (root = merge(x, y)), node[t].k;
	}
}
int n, opt, x;
int main(int argc, char const *argv[]) {
	scanf("%d", &n);

	while (n--) {
		scanf("%d %d", &opt, &x);
		switch(opt) {
			case 1:
				fhqTreap::insert(x);
				break;
			case 2:
				fhqTreap::remove(x);
				break;
			case 3:
				printf("%d\n", fhqTreap::rank(x));
				break;
			case 4:
				printf("%d\n", fhqTreap::search(x));
				break;
			case 5:
				printf("%d\n", fhqTreap::lower(x));
				break;
			case 6:
				printf("%d\n", fhqTreap::upper(x));
				break;
		}
	}
}
```