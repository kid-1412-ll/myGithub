**比赛链接**：<https://codeforces.com/contest/1768>

## A. Greatest Convex

**题意**：给定一个整数$k$，查找小于$k$的最大整数$x$，要求$x!+(x-1)!$是$k$的倍数。

**解法**： $(k-1)!+(k-2)! = (k-1) \times (k-2)!+(k-2)! = k \times (k-2)!$，答案始终为$k-1$。

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

int T;
int n;
int main() {
    scanf("%d", &T);
    while(T--) {
        scanf("%d", &n);
        printf("%d\n", n - 1);
    }
    return 0;
}
```

## B. Quick Sort

**题意**：给定一个长度为$n$的全排列，每次操作选择$k$个元素，删除它们后按递增顺序添加到排列的末尾。求出按升序对排列进行排序所需的最少操作数。

**解法**：假设有$x$个元素不参与任何操作，那么在最终数组中这$x$个元素将按原来的顺序排在数组的开头。最终数组按升序排列，则这$x$个元素在原数组中是形如$[1,2,3...]$的子序列。为了使操作次数最少，则$x$最大。换而言之在原数组中找出形如$[1,2,3...]$的最长子序列，然后从小到大依次删除不在子序列中的元素，最少的操作次数为$\lceil\frac{n - x}{k}\rceil=\lfloor \frac{n - x + k - 1}{k} \rfloor$。

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

int T;
int n, k;
int a[MAXN];
void solve() {
    scanf("%d%d", &n, &k);
    for(int i = 0; i < n; i++) scanf("%d", &a[i]);

    int cnt = 0;//子序列长度
    for(int i = 0; i < n; i++) {
        if(a[i] == cnt + 1) cnt++;
    }

    int ans = (n - cnt + k - 1) / k;
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

## C. Elemental Decompress

**题意**：给定一个包含$n$个整数的数组$a$。需要求出两个全排列$p$和$q$满足$\max(p_i,q_i)=a_i$。

**解法**：一种方式是将$a$中的元素选择性的放入到$p$中或$q$中。从小到大遍历$a$中的元素，如果$a[i]$在$p$中没有被使用且$q$中有小于等于$a[i]$的元素没有被使用，则将$a[i]$放置到$p$中并且$q$选择最小的可以使用的元素。放置在$q$中的情况和$p$一样，如果$p$和$q$均无法放置则无解。

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

int T;
int n;
struct node {
    int id;
    int x;
} a[MAXN];
bool f1[MAXN], f2[MAXN];
int ans1[MAXN], ans2[MAXN];
bool cmp(node a, node b) {
    return a.x < b.x;
}
bool cal() {
    int id1 = 1, id2 = 1;
    for(int i = 0; i <= n; i++) f1[i] = f2[i] = false;

    for(int i = 0; i < n; i++) {
        int target = a[i].x;
        if(!f1[target]) {//尝试放入p中
            while(id2 <= target && f2[id2]) {
                id2++;
            }
            if(id2 <= target) {
                ans1[a[i].id] = target;
                ans2[a[i].id] = id2;
                f1[target] = true;
                f2[id2] = true;
                continue;
            }
        }

        if(!f2[target]) {//尝试放入q中
            while(id1 <= target && f1[id1]) {
                id1++;
            }
            if(id1 <= target) {
                ans1[a[i].id] = id1;
                ans2[a[i].id] = target;
                f1[id1] = true;
                f2[target] = true;
                continue;
            }
        }

        return false;
    }
    return true;
}
void solve() {
    scanf("%d", &n);
    for(int i = 0; i < n; i++) {
        a[i].id = i;
        scanf("%d", &a[i].x);
    }
    sort(a, a + n, cmp);

    if(cal()) {
        puts("YES");
        for(int i = 0; i < n; i++) printf("%d ", ans1[i]);
        putchar(10);
        for(int i = 0; i < n; i++) printf("%d ", ans2[i]);
        putchar(10);
        return;
    }
    puts("NO");
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## D. Lucky Permutation

**题意**：给定一个全排列，每次操作交换任意两个元素，问最少交换多少次使得排列逆序数恰好为1。

**解法**：从有向图的角度来看整个排列，对每个元素建立有向边$i \rightarrow a_i$，整个图由$k$个有向环构成。可以先使整个排列交换成升序，然后交换任意两个相邻的元素来使得排列逆序数恰好为1，这样的方式需要的操作次数为$n-k+1$。但如果一个有向环中存在相邻的两个元素，可以使得这两个相邻元素始终在同一个有向环中，那么最终得到的排列逆序数恰好为1，所需要的操作次数为$n-k-1$。换而言之，如果所有的有向环中都没有两个相邻的元素，那么将整个排列升序然后交换任意两个相邻的元素，否则保留任意一个有向环中两个相邻的元素使其形成逆序对。

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

int T;
int n;
int a[MAXN];
int f[MAXN];
void dfs(int i, int root) {
    do {
        f[i] = root;
        i = a[i];
    } while(i != root);
}
void solve() {
    scanf("%d", &n);
    for(int i = 1; i <= n; i++) {
        scanf("%d", &a[i]);
        f[i] = i;
    }

    int cnt = 0;
    for(int i = 1; i <= n; i++) {
        if(f[i] == i) {//首次遍历环
            cnt++;
            dfs(i, i);
        }
    }

    for(int i = 1; i < n; i++) {
        if(f[i] == f[i + 1]) {//环中存在相邻的元素
            printf("%d\n", n - cnt - 1);
            return;
        }
    }

    printf("%d\n", n - cnt + 1); //环中没有相邻的元素
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## E. Partial Sorting

**题意**：考虑长度为 $3n$ 的排列 $p$。 每次可以进行以下操作之一：按递增顺序对前 $2n$ 个元素进行排序；按递增顺序对最后 $2n$ 个元素进行排序。定义 $f(p)$ 为使排列 $p$ 以递增顺序排序所需操作的最小次数。求所有排列的 $f(p)$ 之和。

**解法**：注意到对于任意排列最多执行3次操作，可以把排列按操作次数的不同分别计数，然后分别计算对答案的贡献即可。

- $f(p) = 0$，有且只有一个排列不需要操作，个数为$1$。
- $f(p) \leq 1$，当前$n$个数已经升序或者后$n$个数已经升序时需要的操作次数不超过1，个数为$2 \times (2n!) - (n!)$。
- $f(p) \leq 2$，当前$n$个数分布于$[1,2n]$中或者后$n$个数分布于$[n+1,3n]$中时需要的操作次数不超过2，个数为$,2 \times C_{2n}^{n} \times n! \times 2n! - \sum_{i = 0}^{n} C_{n}^{n - i} \times C_{n}^{i} \times C_{2n - i}^{n} \times n! \times n! \times n!$。
- $f(p) \leq 3$，所有排列操作次数都不超过3，个数为$3n!$。

```cpp
#include <bits/stdc++.h>
using namespace std;
#define LL long long
const int INF = 0x3f3f3f3f;     ///1 061 109 567
const int negative_infinite = 0xcfcfcfcf;   ///-808 464 433
const double pi = acos(-1);
const LL mod = 1e9 + 7;
const double eps = 1e-8;
const int MAXN = 3e6 + 117;
const int MAXM = 1e6 + 117;

int n, M;
LL a[MAXN];
LL inv[MAXN];//阶乘的逆元
LL fact[MAXN];//阶乘
LL fast_power(LL a, LL n, LL p) {
    LL ans = 1;
    a %= p;
    while(n) {
        if(n & 1) ans = ans * a % p;
        a = a * a % p;
        n >>= 1;
    }
    return ans;
}
void init() {
    fact[0] = 1;
    for(int i = 1; i <= 3 * n; i++) {
        fact[i] = fact[i - 1] * i % M;
    }

    inv[3 * n] = fast_power(fact[3 * n], M - 2, M);
    for (int i = 3 * n - 1; i >= 0; i--) {
        inv[i] = inv[i + 1] * (i + 1) % M;
    }
}
LL sub(LL x, LL y) {
    return ((x - y) % M + M) % M;
}
LL com(int n, int m) {
    return fact[n] * inv[m] % M * inv[n - m] % M;
}
void solve() {
    a[0] = 1;
    a[1] =  sub(2 * fact[2 * n], fact[n]);
    a[2] = 2 * com(2 * n, n) % M * fact[n] % M * fact[2 * n] % M;
    for(int i = 0; i <= n; i++) {
        LL num = com(n, n - i) * com(n, i) % M * com(2 * n - i, n) % M;
        num = num * fact[n] % M * fact[n] % M * fact[n] % M;
        a[2] = sub(a[2], num);
    }
    a[3] = fact[3 * n];

    LL ans = 0;
    for(int i = 3; i > 0; i--) a[i] = sub(a[i], a[i - 1]);
    for(int i = 0; i <= 3; i++) ans = (ans + a[i] * i) % M;
    printf("%lld\n", ans);
}
int main() {
    scanf("%d%d", &n, &M);
    init();
    solve();
    return 0;
}
```

## F. Wonderful Jump

**题意**：给定一个长度为$n$的正整数数组，每一次操作可以花费$\min(a_j \ldots a_i) \cdot (i - j)^2$的代价从$i \rightarrow j$。求从1到达每个位置的最小代价。

**解法**：可以非常容易的得到一个$\mathcal{O}(n^2)$的解决方案$dp_i = dp_j + \min(a_j \ldots a_i) \cdot (i - j)^2$。定义A是所有$a_i$的最大值。

- 如果$\min(a_j \ldots a_i) \cdot (i - j)^2 \gt A \cdot (i - j)^2$，这意味着执行$(i - j)$次变换比直接一次变换所需的代价更低。转换这个不等式可以得到$j - i > \frac{A}{\min(a_i \ldots a_j)}$，这意味着当区间的最小值变大时需要考虑的区间范围随之变小。
- 如果存在$j \lt k \lt i$且$\min(a_j \ldots a_i) = a_k$，则先$j \rightarrow k$再$k \rightarrow i$的方案优于$j \rightarrow i$。

$$
\min(a_j \ldots a_i) \cdot (i - j) ^ 2 > \min(a_j \ldots a_k) \cdot (k - j) ^ 2 + \min(a_k \ldots a_i) \cdot (i - k)^2
\\
a_k \cdot (i - j) ^ 2 > a_k \cdot (k - j) ^ 2 + a_k \cdot (i - k)^2
\\
(i - j) ^ 2 > (k - j) ^ 2 + (i - k)^2
$$

对此把需要考虑的情况分成两类

- $\min(a_j \ldots a_i) = a_i$
  - 如果$a_i \lt \sqrt{A}$，往前遍历直到$a_i$不是区间的最小值。
  - 反之只需要往前遍历最多$\sqrt{A}$个元素即可。
- $\min(a_j \ldots a_i) = a_j$
  - 如果$a_j \lt \sqrt{A}$，维护一个单调栈使得栈中元素$a_j$都是区间$[j,i]$的最小值。
  - 反之只需要往前遍历最多$\sqrt{A}$个元素即可。

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

int n, k = 635;
LL a[MAXN];
LL dp[MAXN];
void solve() {
    dp[0] = 0;
    vector<LL> st = {0};
    for(int i = 1; i < n; i++) {
        LL mi = a[i];
        for(int j = i - 1; j >= max(0, i - k); j--) {
            mi = min(mi, a[j]);
            dp[i] = min(dp[i], dp[j] + mi * (i - j) * (i - j));
        }

        //a[i]是最小值
        if(a[i] < k) {
            for(int j = i - 1; j >= 0; j--) {
                dp[i] = min(dp[i], dp[j] + a[i] * (i - j) * (i - j));
                if(a[j] <= a[i]) break;
            }
        }

        //a[j]是最小值
        for(int j : st) {
            dp[i] = min(dp[i], dp[j] + a[j] * (i - j) * (i - j));
        }
        if(a[i] < k) {
            while(!st.empty() && a[st.back()] >= a[i]) st.pop_back();
            st.push_back(i);
        }
    }
}
int main() {
    scanf("%d", &n);
    for(int i = 0; i < n; i++) {
        scanf("%lld", &a[i]);
        dp[i] = (LL)INF * INF;
    }
    solve();
    for(int i = 0; i < n; i++) printf("%lld ", dp[i]);
    return 0;
}
```

![有趣](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20230216154807.png)