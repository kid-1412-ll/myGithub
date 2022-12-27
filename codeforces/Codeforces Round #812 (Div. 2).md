**比赛链接**：<https://codeforces.com/contest/1713>

## A. Traveling Salesman Problem

**题意**：笛卡尔坐标系中，每次移动可以到达四个相邻点（左、右、上、下）中的任何一个。给定$n$个点，限制在坐标轴上。要求从原点出发经过所有的点回到原点，求最小移动次数。

**解法**：因为限制从原点出发回到原点且点都在坐标轴上，所以只需统计坐标轴上每个方向所需的移动次数即可。 

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

int n;
int x, y;
void solve() {
    scanf("%d", &n);
    int x1 = 0, x2 = 0, y1 = 0, y2 = 0;
    for(int i = 0; i < n; i++) {
        scanf("%d%d", &x, &y);
        x1 = max(x1, x);
        x2 = min(x2, x);
        y1 = max(y1, y);
        y2 = min(y2, y);
    }

    printf("%d\n", (x1 - x2 + y1 - y2) * 2);
}
int main() {
    int T = 1;
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## B. Optimal Reduction

**题意**：一个由 $n$ 个正整数组成的数组 $a$，可以执行以下操作：选择两个索引 $l$ 和 $r (1≤l≤r≤n)$，将所有元素 $a_l,a_{l+1},…,a_r$ 减 1。将 $f(a)$ 定义为把数组 $a$ 中的元素全部修改为零所需要的最小操作次数。确定对于 a 的所有排列† b，f(a)≤f(b) 是否为真。

**解法**：设$M$是数组中的最大值，每次操作使得子数组中的元素减小，因此 $f(a)$ 始终不小于$M$。所有满足$f(a)=M$的数组都可以划分成两个子数组，左边的子数组是一个非递减的数组，右边的子数组是一个非递增的数组。现在只需要检查给出的数组是否满足条件即可。

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

int n;
int a[MAXN];
void solve() {
    scanf("%d", &n);
    for(int i = 0; i < n; i++) scanf("%d", &a[i]);
    int l = 0;
    while(l < n) {
        if(l == n - 1) {
            puts("YES");
            return;
        }
        if(a[l] <= a[l + 1]) l++;
        else break;
    }
    int r = n - 1;
    while(r >= 0) {
        if(r == 0) {
            puts("YES");
            return;
        }
        if(a[r] <= a[r - 1]) r--;
        else break;
    }
    puts(l >= r ? "YES" : "NO");
}
int main() {
    int T = 1;
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
/*
4
3 4 4 2
*/
```

## C. Build Permutation

**题意**：要求构造一个下标从0开始的全排列，使得每一项$i+a_i$的值是一个完全平方数。

**解法**：

- 存在结论：对于非负整数$n$，总存在一个完全平方数$k$，满足$n \leq k \leq 2n$。[证明](https://math.stackexchange.com/questions/1190737/proving-that-there-is-a-perfect-square-between-n-and-2n)$n \leq \lceil \sqrt{n} \rceil^2 \leq 2n$即可。
- 从后往前构造排列。则区间$[k-n,n]$可以使用$k$来计算对应的$a[i]$，对应$k-n$之前的位置可以迭代使用相同的方式计算。

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

int ans[MAXN];
void cal(int n) {
    if(n < 0) return;
    int k = sqrt(n * 2);
    k *= k;
    cal(k - n - 1);
    for(int i = k - n; i <= n; i++) ans[i] = k - i;
}
void solve() {
    int n;
    scanf("%d", &n);
    cal(n - 1);
    for(int i = 0; i < n; i++) printf("%d ", ans[i]);
    putchar(10);
}
int main() {
    int T = 1;
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## D. Tournament Countdown

**题意**：给定一场有$2^n$名选手参加的单淘汰锦标赛，每次询问两个参赛选手，系统将告知哪位选手的胜场更多。要求询问的次数不超过$\left \lceil \frac{1}{3} \cdot 2^{n + 1} \right \rceil$。

**解法**：考虑只有4名选手，对1号和3号选手询问，将会有以下三种情况：

- 1>3：则只有两种情况，1号选手胜两场、3号选手胜一场或零场，1号胜一场、3号胜零场。这样无论哪种情况2号选手和3号选手都不可能是最后的冠军，只需要再询问一次1号选手和4号选手的胜场情况就可以区分冠军。总共两次询问。
- 1=3：和上面同理，1号选手和3号选手不会是最后的冠军，再询问一次2号选手和4号选手即可。
- 1<3：和上面同理，1号选手和4号选手不会是最后的冠军，再询问一次2号选手和3号选手即可。

这样每两次询问可以减少3名参赛选手，最终总的询问次数可以满足条件。

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

int n;
int a[4];
queue<int> q;
int getop(int x, int y) {
    int op;
    printf("? %d %d\n", x, y);
    fflush(stdout);
    scanf("%d", &op);
    return op;
}
void cal(int x, int y) {
    int op = getop(x, y);
    if(op == 1) q.push(x);
    else if(op == 2) q.push(y);
}
void solve() {
    while(!q.empty()) q.pop();
    scanf("%d", &n);
    n = 1 << n;
    for(int i = 1; i <= n; i++) q.push(i);

    while(true) {
        int len = q.size();
        if(len == 1) {
            break;
        }
        if(len == 2) {
            for(int i = 0; i < 2; i++) {
                a[i] = q.front();
                q.pop();
            }
            cal(a[0], a[1]);
            break;
        }

        for(int i = 0; i < 4; i++) {
            a[i] = q.front();
            q.pop();
        }
        int op = getop(a[0], a[2]);
        if(op == 1) {
            cal(a[0], a[3]);
        } else if(op == 2) {
            cal(a[1], a[2]);
        } else {
            cal(a[1], a[3]);
        }
    }

    printf("! %d\n", q.front());
    fflush(stdout);
}
int main() {
    int T = 1;
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## E. Cross Swapping

**题意**：给定一个$n$阶方阵，每次操作交换第$k$行和第$k$列的元素。求可以得到的字典序最小的方阵。

**解法**：并查集、贪心

- 需要字典序最小，则从上到下、从左到右进行贪心。
- 定义集合$s$，对于集合中的任意两个元素$x$和$y$，若交换第$x$行则也交换第$y$行。
- 遍历主对角线以上的元素。若$a[i][j] \lt a[j][i]$，则合并集合$i$和集合$j$。若$a[i][j] \gt a[j][i]$，则合并集合$i$和集合$-j$。若合并失败则表示无法在满足前置条件下将此单元格字典序最小。
- 根据集合的情况进行相应的交换操作然后输出即可。

```cpp
#include <bits/stdc++.h>
using namespace std;
#define LL long long
const int INF = 0x3f3f3f3f;     ///1 061 109 567
const int negative_infinite = 0xcfcfcfcf;   ///-808 464 433
const double pi = acos(-1);
const LL mod = 1e9 + 7;
const double eps = 1e-8;
const int MAXN = 1e3 + 117;
const int MAXM = 1e6 + 117;

int n;
int a[MAXN][MAXN];
int fa[MAXN];
int getFa(int x) {
    if(x < 0) return -getFa(-x);
    if(x == fa[x]) return x;
    return fa[x] = getFa(fa[x]);
}
void addEdge(int x, int y) {
    x = getFa(x);
    y = getFa(y);
    if(abs(x) == abs(y)) return;
    if(x > 0) fa[x] = y;
    if(x < 0) fa[-x] = -y;
}
void solve() {
    scanf("%d", &n);
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
            scanf("%d", &a[i][j]);
        }
    }
    for(int i = 1; i <= n; i++) fa[i] = i;

    for(int i = 1; i <= n; i++) {
        for(int j = i + 1; j <= n; j++) {
            if(a[i][j] < a[j][i]) addEdge(i, j);
            if(a[i][j] > a[j][i]) addEdge(i, -j);
        }
    }

    for(int i = 1; i <= n; i++) {
        if(getFa(i) < 0) continue;
        for(int j = 1; j <= n; j++) {
            swap(a[i][j], a[j][i]);
        }
    }

    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
            printf("%d ", a[i][j]);
        }
        putchar(10);
    }
}
int main() {
    int T = 1;
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## F. Lost Array

#### Hint 0

有没有答案不存在的情况？如果存在，是否有多个？

答案一定存在，且唯一。根据异或运算的性质有 $b_{i,j-1} = b_{i,j} \oplus b_{i-1,j}$，那可以从矩阵的右下角往左上角依次反推出整个矩阵，从而求出唯一解。

#### Hint 1

如何计算 $a_i$ 对 $b_{j,n}$ 的贡献值？

类似于[Pascal's Triangle(杨辉三角)](https://en.wikipedia.org/wiki/Pascal%27s_triangle)的计算，$a_i$ 对 $b_{j,n}$ 贡献的次数为 $\binom{(n-i) + (j-1)}{j-1}$。根据异或运算的性质当 $a_i$ 出现奇数次时对 $b_{j,n}$ 的贡献为 $a_i$，否则为0。

#### Hint 2

先考虑逆向问题：给定数组 a，构造所有的 $b_{j,n}$，怎么快速解决这个问题？

根据[Lucas Theorem](https://en.wikipedia.org/wiki/Lucas's_theorem)，当 $(n - i)\ AND\ (j - 1) = 0$ 时 $\binom{(n-i) + (j-1)}{j-1}$ 为奇数 $\rightarrow (n - i)$ 是 $\sim(j - 1)$ 的子码。定义 $m$ 是满足 $m = 2^k$ 且 $m \ge n$ 的最小值，令 $b'_j = b_{\sim(j - 1)}$，则 $b'$ 是 $a$ 的 [Zeta transform](https://codeforces.com/blog/entry/45223)，可以使用 [SOS DP](http://www.kindkidll.com/index.php/archives/210/) 解决。
$$
b_j 
= \sum_{n-i \subseteq \sim(j-1)} a_i 
= \sum_{i \subseteq \sim(j-1)} a_{n-i} \\

b'_j 
= b_{\sim(j - 1)} 
= b_{(m - 1) - (j - 1)} 
= \sum_{i \subseteq \sim((m - 1) - j)} a_{n-i}
= \sum_{i \subseteq j} a_{n-i}
$$

#### Hint 3

先考虑更简单的问题：用和矩阵 $b$ 相同的方式构造大小为 $(2n + 1) \times (n + 1)$ 的矩阵 $g$，怎么求解数组 $a$？

令$g'_j = g_{\sim(j - 1)}$，可以在矩阵 $g'$ 上使用 [Mobius transform](https://codeforces.com/blog/entry/72488) 来得到数组 $a$，由于是异或操作，mobius transform和zeta transform是相同的。
$$
a_{n-j} = \sum_{i \subseteq j} g'_i
$$

#### 解决

结合Hint 2和Hint 3即可解决这个问题，但有一点不同的是，我们并不知道 $b'$ 在 $[0,m-n)$ 中的值。zeta transform可以看作一个高维前缀和，使用容斥原理可以从从高维前缀和中还原出单点的值，具体看[整除偏序的结构、zeta变换、Möbius变换、lcm卷积、gcd卷积](https://www.luogu.com.cn/paste/ljlnucke)这篇文章。在这里可以执行以下操作得到 $b'$ 的值。
$$
c_j =
\begin{cases}
0, \quad 0 \leq i \lt m-n \\
\sum_{j \subseteq i} b'_i, \quad m-n \leq i \lt m
\end{cases}

\quad  \rightarrow \quad 

b'_j = \sum_{j \subseteq i} c_i
$$
具体步骤如下：

- 定义 $b'_j = b_{\sim(j - 1)}$ 并计算所有区间内的值。
- 对 $b'$ 使用mobius transform得到 $a'$。
- 由 $a'$ 得到 $a$。

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

int n;
int m, lm, all1;
int a[MAXN], b[MAXN];
int tb[MAXN], c[MAXN];
void buildB() {
    //原数组 b 只有[1,n]有值
    for(int i = 1; i <= n; i++) tb[all1 ^ (i - 1)] = b[i];

    //先翻转变成子集求和问题
    for(int i = 0; i < n; i++) {
        c[i] = tb[i ^ all1];
    }
    //第一次求和
    for(int i = 0; i < lm; i++) {
        for(int mask = 0; mask < n; mask++) {
            if((mask >> i) & 1) {
                c[mask] ^= c[mask ^ (1 << i)];
            }
        }
    }
    //获取翻转后的b'
    for(int i = 0; i < lm; i++) {
        for(int mask = 0; mask < m; mask++) {
            if((mask >> i) & 1) {
                c[mask] ^= c[mask ^ (1 << i)];
            }
        }
    }
    //获取b'
    for(int i = 0; i < m; i++) {
        tb[i] = c[i ^ all1];
    }
}
int main() {
    scanf("%d", &n);
    m = 1 << __lg(n);
    if(m < n) m <<= 1;
    lm = __lg(m);
    all1 = m - 1;

    for(int i = 1; i <= n; i++) scanf("%d", &b[i]);
    buildB();

    //mobius transform
    for(int i = 0; i < lm; i++) {
        for(int mask = 0; mask < m; mask++) {
            if((mask >> i) & 1) {
                tb[mask] ^= tb[mask ^ (1 << i)];
            }
        }
    }

    for(int i = 1; i <= n; i++) a[i] = tb[n - i];
    for(int i = 1; i <= n; i++) printf("%d ", a[i]);
    return 0;
}
```

