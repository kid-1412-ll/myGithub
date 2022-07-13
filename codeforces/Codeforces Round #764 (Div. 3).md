**比赛链接**：<http://codeforces.com/contest/1624>

## A. Plus One on the Subset

**题意**：给定一个集合$a$，每次操作选取集合中任意个元素增加1，求最少多少次操作使得集合中的值全都相等。

**解法**：首先增加集合中的最大值总会增加操作次数，我们每次操作选取集合中和最大值不相等的数，所需要的操作次数为$max(a)-min(a)$。

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
 
int t;
int n;
int a[107];
int main() {
    scanf("%d", &t);
    while(t--) {
        int mi = INF, ma = -100;
        scanf("%d", &n);
        for(int i = 0; i < n; i++) {
            scanf("%d", &a[i]);
            ma = max(ma, a[i]);
            mi = min(mi, a[i]);
        }
        printf("%d\n", ma - mi);
    }
    return 0;
}
```

## B. Make AP

**题意**：给定一个正整数三元组，对其中一个元素乘以一个正整数$m$，使其成为一个等差数列，求$m$是否有解？

**解法**：枚举三种情况即可，注意判断整数除法和边界条件。

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

int t;
int a, b, c;

bool check() {
    if(a + c >= 2 * b && (a + c) % (2 * b) == 0) return true;
    if(2 * b > c && 2 * b - c >= a && (2 * b - c) % a == 0) return true;
    if(2 * b > a && 2 * b - a >= c && (2 * b - a) % c == 0) return true;
    return false;
}

int main() {
    scanf("%d", &t);
    while(t--) {
        scanf("%d%d%d", &a, &b, &c);
        if(check()) puts("YES");
        else puts("NO");
    }
    return 0;
}
```

## C. Division by Two and Permutation

**题意**：给定一个正整数集合，每次操作选择一个元素除以2(整数除法)，问能否得到一个排列？

**解法1(二分图)**：将每个元素$a[i]$和能得到的值(1到n)建边，显然这是一个二分图最大匹配问题，我们可以使用匈牙利算法解决这个问题。

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

int t;
int n;
int a[177];
vector<int> ma[177];
int match[177];
bool vis[177];

int dfs(int i) {
    for(int j = 0; j < ma[i].size(); j++) {
        int ne = ma[i][j];
        if(!vis[ne]) {
            vis[ne] = true;
            if(match[ne] == 0 || dfs(match[ne])) {
                match[i] = ne;
                match[ne] = i;
                return 1;
            }
        }
    }
    return 0;
}
int main() {
    scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        for(int i = 0; i <= 2 * n; i++) ma[i].clear();
        for(int i = 1; i <= n; i++) {
            scanf("%d", &a[i]);
            while(a[i]) {
                if(a[i] <= n) {
                    ma[a[i]].push_back(i + n);
                    ma[i + n].push_back(a[i]);
                }
                a[i] /= 2;
            }
        }

        memset(match, 0, sizeof(match));
        int cnt = 0;
        for(int i = 1; i <= n; i++) {
            if(match[i] == 0) {
                memset(vis, false, sizeof(vis));
                cnt += dfs(i);
            }
        }
        if(cnt == n) puts("YES");
        else puts("NO");
    }
    return 0;
}
```

**解法2(贪心)**：将元素按从大到小的顺序贪心，使用$vis[i]$标记数字$i$是否已经被得到，如果$a[i]>n$或者$a[i]$已经被得到，将$a[i]$除以2，若$a[i]=0$则无法构造排列。

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

int t;
int n;
int a[177];
bool vis[177];
int main() {
    scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        memset(vis, false, sizeof(vis));
        bool f = true;
        for(int i = 1; i <= n; i++) {
            scanf("%d", &a[i]);
            while(a[i] >= 0) {
                if(!f) break;
                while(a[i] > n || vis[a[i]]) a[i] /= 2;
                if(a[i] == 0) f = false;
                vis[a[i]] = true;
                break;
            }
        }
        if(f) puts("YES");
        else puts("NO");
    }
    return 0;
}
```

## D. Palindromes Coloring

**题意**：给定一个由小写字母构成的字符串$s$和$1 \leq k \leq 2 \cdot 10^5$种颜色，现在用$k$种颜色给字符串涂色，要求每种颜色至少有$1$个字母，但不一定需要所有的字母都被涂色。被涂上同一种颜色的字母可以任意交换顺序，现在要求由同一种颜色构成的子序列是回文串。求一种涂色方式，使得$k$个回文串长度的最小值尽可能的大。

**解法**：首先，对于任意由字符串$s$得到的$k$个回文串，总存在一种涂色方式使其可以被构造。即我们不需要关心怎么去涂色，而只需要把$k$个回文串构造出来即可。对此我们可以采用如下的构造方式，在$k$个回文串中，拥有相同长度的任意回文串是等价的，即$abba$和$cccc$等价。统计26个字母两两匹配的总数量再除以$k$，即是单个回文串的基础匹配数量。注意到此时构造的回文串长度均是偶数，如果剩余的字母数量不少于$k$，则可以往每个回文串中间插入一个字母来得到最终的结果。

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

int t;
int n, k;
char s[MAXN];
int cnt[27];

int main() {
    scanf("%d", &t);
    while(t--) {
        memset(cnt, 0, sizeof(cnt));
        scanf("%d%d%s", &n, &k, s);
        for(int i = 0; i < n; i++) cnt[s[i] - 'a']++;

        int sum = 0;
        for(int i = 0; i < 26; i++) sum += cnt[i] / 2;
        int ans = sum / k * 2;
        if(n - ans * k >= k) ans++;

        printf("%d\n", ans);
    }
    return 0;
}
```

## E. Masha-forgetful

**题意**：给定$n$个由0-9构成的长度为$m$的字符串，再给定一个目标串。将目标串拆分成若干个长度大于1的子串，要求这些子串存在于给定的$n$个字符串中。输出每个子串对应字符串的范围。

**解法**：核心思想是：任意长度大于3的子串都可以再次被拆分成若干个长度为2或3的字符串。设$dp[i]$表示前缀$i$可以通过$dp[i]$构成，则当$dp[i-2]$成立且$s[i-1]s[i]$这个子串存在时$dp[i]=i-2$，当$dp[i-3]$成立且$s[i-2]s[i-1]s[i]$这个子串存在时$dp[i]=i-3$。

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

int t;
int n, m;
char s[1077];
struct node {
    int l, r, id;
} a[10707];
int dp[1077];
vector<int> ans;
int f2(int j) {
    j--;
    return (s[j - 1] - '0' + 1) * 10 + s[j] - '0' + 1;
}
int f3(int j) {
    j--;
    return (s[j - 2] - '0' + 1) * 10 * 10 + (s[j - 1] - '0' + 1) * 10 + s[j] - '0' + 1;
}
int main() {
    scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        for(int i = 0; i < 10000; i++) a[i].id = -1;
        for(int i = 1; i <= n; i++) {
            scanf("%s", s);
            for(int j = 1; j <= m; j++) {
                if(j >= 2) {
                    int index = f2(j);
                    if(a[index].id == -1) {
                        a[index].l = j - 1;
                        a[index].r = j;
                        a[index].id = i;
                    }
                }
                if(j >= 3) {
                    int index = f3(j);
                    if(a[index].id == -1) {
                        a[index].l = j - 2;
                        a[index].r = j;
                        a[index].id = i;
                    }
                }
            }
        }

        scanf("%s", s);
        for(int j = 0; j <= m; j++) dp[j] = -1;
        if(m >= 2 && a[f2(2)].id != -1) dp[2] = f2(2);
        if(m >= 3 && a[f3(3)].id != -1) dp[3] = f3(3);
        for(int j = 4; j <= m; j++) {
            int index = f2(j);
            int len = a[index].r - a[index].l + 1;
            if(a[index].id != -1 && dp[j - len] != -1) {
                dp[j] = index;
                continue;
            }

            index = f3(j);
            len = a[index].r - a[index].l + 1;
            if(a[index].id != -1 && dp[j - len] != -1) {
                dp[j] = index;
                continue;
            }
        }

        bool f = true;
        ans.clear();
        for(int j = m; j > 0;) {
            if(dp[j] != -1) {
                ans.push_back(dp[j]);
                j -= a[dp[j]].r - a[dp[j]].l + 1;
            } else {
                f = false;
                break;
            }
        }

        if(f) {
            printf("%d\n", ans.size());
            for(int i = ans.size() - 1; i >= 0; i--) {
                printf("%d %d %d\n", a[ans[i]].l, a[ans[i]].r, a[ans[i]].id);
            }
        } else puts("-1");
    }
    return 0;
}
```

## F. Interacdive Problem

**题意**：交互式的题目，给出一个$n(2 \lt n \le 1000)$，你需要去猜一个数字$x(1 \le x \lt n)$。你每次给出一个数字$c$，然后执行操作$x=x+c(1 \le c \lt n)$，然后会给出$\lfloor \frac{x}{n} \rfloor$ 的结果$ans$，你需要使用不超过10次操作来猜到数字$x$，输出最终的结果。

**解法**：二分求解。若当前取值区间为$[l,r]$，取$c=(ans+1)*n- \lfloor \frac{l+r+1}{2} \rfloor$。若结果$ans$没有增加，则说明取值在左区间，否则取值在右区间，正确的处理区间直到取值范围唯一为止。

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
int main() {
    scanf("%d", &n);

    int ans = 0, num;
    int l = 1, r = n - 1;
    while(l < r) {
        int mid = (ans + 1) * n - (l + r + 1) / 2;
        printf("+ %d\n", mid);
        fflush(stdout);
        scanf("%d", &num);

        if(ans == num) {
            l = l + mid;
            r = min(r + mid, (ans + 1) * n - 1);
        } else {
            ans = num;
            l = max(l + mid, ans * n);
            r = r + mid;
        }
    }

    printf("! %d\n", l);
    fflush(stdout);
    return 0;
}
```

## G. MinOr Tree

**题意**：给定一张$n$个顶点$m$条边的连通加权无向图，通过选择一颗生成树，使得该生成树边权值按位或的值尽可能的小，求最小的生成树权值。

**解法**：将边权值按二进制从高位到低位进行贪心，如果排除掉所有权值二进制第$i$位为1的边后，该图仍然连通，则最小生成树的权值第$i$位为0，否则第$i$位为1。

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

int t;
int ans = 0;
int n, m;
int u, v, w;
int head[MAXN], tot;
struct node {
    int v;
    int w;
    int ne;
} edge[MAXN];

void addedge(int u, int v, int w) {
    edge[tot].v = v;
    edge[tot].w = w;
    edge[tot].ne = head[u];
    head[u] = tot++;
}

int cnt = 0;
bool vis[MAXN];
void dfs(int id) {
    if(vis[id]) return;
    cnt++;
    vis[id] = true;
    for(int i = head[id]; i != -1; i = edge[i].ne) {
        if((edge[i].w | ans) == ans) {
            dfs(edge[i].v);
        }
    }
}
void solve(int index) {
    if(index < 0) return;

    cnt = 0;
    for(int i = 0; i <= n; i++) vis[i] = false;
    int num = 1;
    for(int i = 0; i < index; i++) num <<= 1;
    ans -= num;

    dfs(1);
    if(cnt < n) ans += num;

    solve(index - 1);
}
int main() {
    scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);

        tot = 0;
        for(int i = 0; i <= n; i++) head[i] = -1;

        for(int i = 0; i < m; i++) {
            scanf("%d%d%d", &u, &v, &w);
            addedge(u, v, w);
            addedge(v, u, w);
        }

        ans = 1;
        int index = 0;
        for(; ans < INF; index++) ans = ans * 2 + 1;
        solve(index);
        printf("%d\n", ans);
    }
    return 0;
}
```

