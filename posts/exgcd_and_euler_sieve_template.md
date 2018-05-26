---
title: 欧几里得算法、欧拉筛模板
date: 2018-04-06 19:02:20
tags: [OI,数论,算法模板]
toc: true
---
## 扩展欧几里得
```cpp
int gcd(int const a, int const b) {
    return !b ? a : gcd(b, a % b);
}

int lcm(int const a, int const b) {
    return a / gcd(a, b) * b;
}

int exgcd(int const a, int const b, int &x, int &y) {
    int ans = a;
    if (b == 0) {
        x = 1, y = 0;
        return ans;
    }
    else {
        ans = exgcd(b, a % b, y, x);
        y -= x * (a / b);
        return ans;
    }
}
```

## 欧拉筛
```cpp
bool is_prime[MAXN];
int primes[MAXN], query[MAXM];
int n, m, p;

void sieve(int const n) {
    memset(primes, 0, sizeof(primes));
    memset(is_prime, 1, sizeof(is_prime));
    is_prime[1] = false;
    for (int i = 2; i <= n; i++) {
        if (is_prime[i]) {
            primes[p++] = i;
        }
        for (int j = 0; j < p && i * primes[j] <= n; j++) {
            is_prime[ i * primes[j] ] = false;
            if (! (i % primes[j]) )
                break;
        }
    }
}
```