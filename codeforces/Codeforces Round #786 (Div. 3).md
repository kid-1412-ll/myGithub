**比赛链接**：<https://codeforces.com/contest/1674>

## A. Number Transformation

**题意**：给定$1 \leq x,y \leq 100$，求一对正整数$a,b$满足$x \times b^a = y$。

**解法**：因为$x,y$的范围比较小，直接枚举所有可能的$a,b$组合。

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
int x, y;
int ans[107];
int main() {
    for(int b = 1; b <= 100; b++) {
        int num = 1;
        for(int a = 1; a <= 10; a++) {
            num *= b;
            if(num > 100) break;
            ans[num] = b * 1000 + a;
        }
    }
    scanf("%d", &T);
    while(T--) {
        scanf("%d%d", &x, &y);
        if(y % x != 0) {
            puts("0 0");
            continue;
        }
        printf("%d %d\n", ans[y / x] % 1000, ans[y / x] / 1000);
    }
    return 0;
}
```

## B. Dictionary

**题意**：定义长度为2的字符串有以下映射规则：$ab=1$、$ac=2$...$az=25$、$ba=26$、$bc=27$...$zx=649$、$zy=650$。现给出字符串，要求输出对应的序号。

**解法**：字符串限制只有两位，提前枚举好每个字符串对应的序号即可。

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
string s;
map<string, int> ma;
int main() {
    int cnt = 0;
    for(int i = 0; i < 26; i++) {
        for(int j = 0; j < 26; j++) {
            if(i == j) continue;
            cnt++;
            s = "";
            s.append(1, 'a' + i);
            s.append(1, 'a' + j);
            ma[s] = cnt;
        }
    }
    scanf("%d", &T);
    while(T--) {
        cin >> s;
        cout << ma[s] << '\n';
    }
    return 0;
}
```

## C. Infinite Replacement

**题意**：给定一个只由字符$a$构成的字符串$s$，和一个只由小写字母构成的字符串$t$，现在可以将$s$中的字符$a$替换成$t$，问最多可以得到多少种不同的字符串？

**解法**：

- 当$t$只有一个字符$a$时，无论怎么替换都只能得到原字符串$s$。
- 当$t$长度大于1且包含字符$a$时，可以不断的替换得到无限种的字符串$s$。
- 当$t$长度大于1且不包含字符$a$时，可以得到的字符串种类为$2^n$，$n$为字符串$s$的长度。

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
char s[77], t[77];
LL solve() {
    int lent = strlen(t);
    if(lent == 1 && t[0] == 'a') {
        return 1;
    }
    for(int i = 0; i < lent; i++) {
        if(t[i] == 'a') return -1;
    }

    LL ans = 1;
    int lens = strlen(s);
    for(int i = 0; i < lens; i++) {
        ans *= 2;
    }
    return ans;
}
int main() {
    scanf("%d", &T);
    while(T--) {
        scanf("%s%s", s, t);
        printf("%lld\n", solve());
    }
    return 0;
}
```

## D. A-B-C Sort

**题意**：给定长度为$n(1 \leq n \leq 2 \times 10^5)$的数组a。第一步：当a不为空时，从a中取出最后一个元素并将其移动到数组b的中间。如果b当前为奇数长度，可以选择：将a中的元素放在b的中间元素的左侧或右侧。第二步：当b不为空时，从b中取出中间元素并将其移动到数组c的末尾。如果b当前具有偶数长度，您可以选择取两个中间元素中的哪一个。问能不能通过这两步操作得到的数组c是非递减的？

**解法**：经过两步操作，每一对$(a_n-2i-1,a_n-2i)$总会被移动到$(c_n-2i-1,c_n-2i)$或者$(c_n-2i,c_n-2i-1)$。遍历一遍数组把每一对组合排好序并检查整个数组是否有序即可。

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
bool solve() {
    for(int i = 1 + n % 2; i < n; i += 2) {
        if(a[i] < a[i - 1]) swap(a[i], a[i - 1]);
        if(i - 2 >= 0 && a[i - 1] < a[i - 2]) {
            return false;
        }
    }
    return true;
}
int main() {
    scanf("%d", &T);
    while(T--) {
        scanf("%d", &n);
        for(int i = 0; i < n; i++) scanf("%d", &a[i]);
        puts(solve() ? "YES" : "NO");
    }
    return 0;
}
```

## E. Breaking the Wall

**题意**：给定$n(2 \leq n \leq 2 \times 10^5)$堵墙，每堵墙都有对应的耐久值。每次选择一个目标位置进行操作，目标位置耐久度-2，目标相邻的位置耐久度-1。对每个位置可以进行不限次数的操作，问最少多少次操作可以使得两堵墙耐久度不高于0。

**解法**：

- 第一种情况：每次都选择要打破的墙为中心进行操作。这时应该选择耐久度最低的两面墙，设耐久度最低的墙为$x$，次低的墙为$y$，所需要的次数为$\lceil \frac{x}{2} \rceil + \lceil \frac{y}{2} \rceil$。
- 第二种情况：要打破的墙相隔一个单位，假设为$a_i$和$a_{i+2}$。这时可以选择对$a_{i+1}$进行操作，对目标墙耐久度各-1，也可以选择其中一个目标墙操作，对目标墙耐久度-2。这时每次的操作总能对目标墙总体耐久度-2，那么所需要的次数为$\lceil \frac{a_i + a_{i+2}}{2} \rceil$。
- 第三种情况：要打破的墙相邻，假设为$a_i$和$a_{i+1}$，且$a_i \ge a_{i+1}$。
  - 如果$a_i \ge a_{i+1} \times 2$，那么只需要一直选择$a_i$为目标操作即可，所需要的次数为$\lceil \frac{a_i}{2} \rceil$。
  - 如果$a_i \lt a_{i+1} \times 2$，先选择$a_i$为目标操作$a_i-a_{i-1}$次，这时两个目标墙耐久度都剩余$2 \times a_{i-1} - a_i$。再选择两个目标墙进行操作，每次的操作总能对目标墙总体耐久度-3，那么所需要的次数为$a_i-a_{i-1} + \lceil \frac{2 \times a_{i-1} - a_i + 2 \times a_{i-1} - a_i}{3} \rceil$。

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
int ans = INF;
void solve1(int x, int y) {
    if(x < y) swap(x, y);
    if(x >= y * 2) ans = min(ans, (x + 1) / 2);
    else ans = min(ans, x - y + ((2 * y - x) * 2 + 2) / 3);
}
void solve2(int x, int y) {
    ans = min(ans, (x + y + 1) / 2);
}
int main() {
    scanf("%d", &n);
    int minimum = INF, cmini = INF;
    for(int i = 0; i < n; i++) {
        scanf("%d", &a[i]);
        if(i >= 1) solve1(a[i - 1], a[i]);
        if(i >= 2) solve2(a[i - 2], a[i]);
        if(a[i] < minimum) {
            cmini = minimum;
            minimum = a[i];
        } else if(a[i] < cmini) cmini = a[i];
    }
    ans = min(ans, (minimum + 1) / 2 + (cmini + 1) / 2);
    printf("%d\n", ans);
    return 0;
}
```

## F. Desktop Rearrangement

**题意**：给定一个二维矩阵，每次操作可以将任意两个方格交换位置。现在有多次询问，每次询问给出一个坐标，如果坐标上是`*`则替换成`.`，反之亦然。对于每次询问求出最少多少次操作可以把所有的$*$移到最左边(从左到右、从上到下)。

**解法一**：定义矩阵当前总共有$sum$个`*`，前$sum$个格子(从左到右、从上到下)有$cnt$个`*`，那么需要操作的次数为$sum-cnt$。将二维矩阵按列方向转化为一维，问题转换为单点修改区间求和，使用树状数组维护即可。

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

int n, m, q;
int i, j, sum = 0;
int tree[MAXN];
int lowbit(int x) {
    return x & -x;
}
void update(int p, int x) {
    while(p < MAXN) {
        tree[p] += x;
        p += lowbit(p);
    }
}
int query(int p) {
    int sum = 0;
    while(p) {
        sum += tree[p];
        p -= lowbit(p);
    }
    return sum;
}
char s[1077][1077];
int main() {
    scanf("%d%d%d", &n, &m, &q);
    for(i = 0; i < n; i++) scanf("%s", s[i]);

    for(j = 0; j < m; j++) {
        for(i = 0; i < n; i++) {
            if(s[i][j] == '*') {
                sum++;
                update(j * n + i + 1, 1);
            }
        }
    }

    while(q--) {
        scanf("%d%d", &i, &j);
        i--, j--;
        if(s[i][j] == '*') {
            sum--;
            s[i][j] = '.';
            update(j * n + i + 1, -1);
        } else {
            sum++;
            s[i][j] = '*';
            update(j * n + i + 1, 1);
        }
        printf("%d\n", sum - query(sum));
    }
    return 0;
}
```

**解法二**：直接考虑每次查询对结果造成的影响。定义矩阵当前总共有$sum$个`*`，前$sum$个格子(从左到右、从上到下)有$cnt$个`*`，那么需要操作的次数为$sum-cnt$。

- 如果当前查询使$sum+1$，则$cnt$的值受新增加进前$sum$个的格子和查询修改的格子两者共同影响。
- 如果当前查询使$sum-1$，则$cnt$的值受被移出前$sum$个的格子和查询修改的格子两者共同影响。

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

int n, m, q;
int sum = 0, cnt = 0;
char s[1077][1077];
int main() {
    scanf("%d%d%d", &n, &m, &q);
    for(int i = 0; i < n; i++) {
        scanf("%s", s[i]);
        for(int j = 0; j < m; j++) {
            if(s[i][j] == '*') sum++;
        }
    }

    for(int j = 0; j < m; j++) {
        if(j * n  + 1 > sum) break;
        for(int i = 0; i < n; i++) {
            if(j * n + i + 1 > sum) break;
            if(s[i][j] == '*') cnt++;
        }
    }

    int x, y;
    while(q--) {
        scanf("%d%d", &x, &y);
        x--, y--;
        if(s[x][y] == '*') {
            sum--;
            if(s[sum % n][sum / n] == '*') cnt--;
            if(y * n + x + 1 <= sum) cnt--;
            s[x][y] = '.';
        } else {
            if(s[sum % n][sum / n] == '*') cnt++;
            sum++;
            if(y * n + x + 1 <= sum) cnt++;
            s[x][y] = '*';
        }
        printf("%d\n", sum - cnt);
    }
    return 0;
}
```

## G. Remove Directed Edges

**题意**：给定一个无环、无重边的有向图，有$n(1 \leq n \leq 2 \times 10^5)$个顶点、$m(0 \leq m \leq 2 \times 10^5)$条边。先删除一些边，使得所有顶点的入度和出度都减小或者为0。再选择一个集合$s$包含一些顶点，使得集合中任意两个顶点$u$、$v$存在由$u$到$v$或者由$v$到$u$的路径。求集合$s$的最大大小？

**解法**：对于集合$s$，存在顶点$head$和顶点$tail$，有一条由$head$到$tail$的路径，集合$s$中的所有顶点都在这条路径上。那么对于整张有向图，删除不在$head$到$tail$路径上的所有边是可以得到集合$s$的。现在问题由寻找一个集合$s$变成寻找一条路径，且路径具有以下的特点。

- 路径起始顶点$head$的出度$out[head] \ge 2$。
- 路径中间顶点$i$的入度$in[i] \ge 2$、出度$out[i] \ge 2$。
- 路径结束顶点$tail$的入度$in[tail] \ge 2$。

定义$dp[i]$表示顶点$i$为中间顶点的路径往后最长有多长，则有$dp[i]=max(dp[j])+1$，其中顶点$j$是顶点$i$的儿子。记忆化搜索一下可以得到所有的$dp[i]$，然后枚举一下所有可能的起始顶点选择最长的路径就是集合$s$的顶点。

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

int n, m;
int in[MAXN], out[MAXN];
int head[MAXN], tot;
struct Edge {
    int v;
    int ne;
} edge[MAXN];
void addEdge(int u, int v) {
    edge[tot].v = v;
    edge[tot].ne = head[u];
    head[u] = tot++;
}
int dp[MAXN];//非头结点的长度
int dfs(int i) {
    if(dp[i] != 0) return dp[i];
    if(in[i] >= 2) {
        if(out[i] >= 2) {//中间结点
            for(int j = head[i]; j != -1; j = edge[j].ne) {
                dp[i] = max(dp[i], dfs(edge[j].v) + 1);
            }
        } else {//尾结点
            dp[i] = 1;
        }
    }
    return dp[i];
}
int main() {
    scanf("%d%d", &n, &m);
    for(int i = 0; i <= n; i++) {
        dp[i] = 0;
        head[i] = -1;
        in[i] = out[i] = 0;
    }

    while(m--) {
        int u, v;
        scanf("%d%d", &u, &v);
        addEdge(u, v);
        in[v]++;
        out[u]++;
    }

    int ans = 1;
    for(int i = 1; i <= n; i++) {
        if(out[i] >= 2) {
            for(int j = head[i]; j != -1; j = edge[j].ne) {
                ans = max(ans, dfs(edge[j].v) + 1);
            }
        }
    }
    printf("%d\n", ans);
    return 0;
}
```

