**比赛链接**：<https://codeforces.com/contest/1847>

## A. The Man who became a God

**题意**：给定 $n$ 个正整数，定义 $f(l,r) = |a_l - a_{l+1}| + |a_{l + 1} - a_{l + 2}| + \ldots + |a_{r-1} - a_r|$。将数组分成 $k$ 段，求 $f(1, r_1) + f(r_1 + 1, r_2) + \ldots + f(r_{k-1} + 1, n)$ 的最小值。

**解法**：有 $f(1,m) = f(1,i) + |b_i - b_{i+1}| + f(i+1,m)$，找出最大的 $k$ 个 $|b_i - b_{i+1}|$，以对应的位置进行分段即可。

```cpp
#include <bits/stdc++.h>
using namespace std;
#define LL long long
const int INF = 0x3f3f3f3f;     ///1 061 109 567
const int negative_infinite = 0xcfcfcfcf;   ///-808 464 433
const double pi = acos(-1);
const LL mod = 1e9 + 7;
const double eps = 1e-8;
const int MAXN = 1e6 + 117;
const int MAXM = 1e6 + 117;

int T = 1;
int n, k;
int a[MAXN];
void solve() {
    scanf("%d%d", &n, &k);
    for(int i = 0; i < n; i++) scanf("%d", &a[i]);
    for(int i = 0; i < n - 1; i++) a[i] = abs(a[i] - a[i + 1]);
    sort(a, a + n - 1);
    int ans = 0;
    for(int i = 0; i < n - k; i++) ans += a[i];
    printf("%d\n", ans);
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## B. Hamon Odyssey

**题意**：给定 $n$ 个正整数，定义 $f(l,r) = a_l \and a_{l+1} \and a_{l+2} \and \ldots \and a_r$。。将数组分成 $k$ 段，要求 $f(1, r_1) + f(r_1 + 1, r_2) + \ldots + f(r_{k-1} + 1, n)$ 为最小值时求 $k$ 的最大值。

**解法**：

```cpp

```

## C. Vampiric Powers, anyone?

**题意**：

**解法**：

```cpp

```

## D. Professor Higashikata

**题意**：

**解法**：

```cpp

```

## E. Triangle Platinum?

**题意**：

**解法**：

```cpp

```

## F. The Boss's Identity

**题意**：

**解法**：

```cpp

```