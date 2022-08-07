**比赛链接**：<https://codeforces.com/contest/1714>

## A. Everyone Loves to Sleep

**题意**：给定一天中的$n$个目标时间点和一个起始时间点，问起始时间点到下一个目标时间点经过了多久。

**解法**：排序之后找到下一个目标时间点并计算时间差即可，需要注意跨天的情况。

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
int x, a[MAXN];
int h, m;
void solve() {
    scanf("%d%d%d", &n, &h, &m);
    x = h * 60 + m;
    for(int i = 0; i < n; i++) {
        scanf("%d%d", &h, &m);
        a[i] = h * 60 + m;
    }
    sort(a, a + n);
    a[n] = a[0] + 24 * 60;
    for(int i = 0; i <= n; i++) {
        if(x <= a[i]) {
            int ans = a[i] - x;
            printf("%d %d\n", ans / 60, ans % 60);
            break;
        }
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

## B. Remove Prefix

**题意**：给定一个数组，求一个最长后缀满足没有重复的值。

**解法**：从前往后遍历并标记每个值出现的次数，当某个值重复出现时结束。

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
bool f[MAXN];
void solve() {
    scanf("%d", &n);
    for(int i = 1; i <= n; i++) {
        scanf("%d", &a[i]);
        f[i] = false;
    }
    for(int i = n; i > 0; i--) {
        if(f[a[i]]) {
            printf("%d\n", i);
            return;
        }
        f[a[i]] = true;
    }
    puts("0");
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

## C. Minimum Varied Number

**题意**：找到具有给定数字总和$s$的最小数字，使得其中的所有数字都是不同的。例如，如果$s=20$，则答案为 389。这是所有数字不同且数字之和为20(3+8+9=20)的最小数字。

**解法**：~~范围才45暴力枚举啊(手动狗头)。~~这题有很多的处理方式，比如枚举出每种长度的最大值，例如89对应$s=17$，则$s=14$时的数字为$89-(17-14) \times 10=59$。

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
int a[9] = {9, 17, 24, 30, 35, 39, 42, 44, 45};
void solve() {
    scanf("%d", &n);
    for(int i = 0; i < 9; i++) {
        if(n <= a[i]) {
            int num = 9, beishu = 1, sum = 9;
            for(int j = 0; j < i; j++) {
                num--;
                beishu *= 10;
                sum += num * beishu;
            }
            sum -= (a[i] - n) * beishu;
            printf("%d\n", sum);
            break;
        }
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

## D. Color with Occurrences

**题意**：给定一个字符串$t(1≤|t|≤100)$，和$n(1≤n≤10)$个字符串$s1,s2,…,sn(1≤|si|≤10)$。每次操作选择$t$中的任意一个$s_i$子串，并将相应的字符涂成红色。现在要求将字符串$t$的所有字符涂色，问最少需要操作多少次并给出具体解决方案。

**解法**：注意$t、n、S_i$的数据限制。定义$dp[i]$表示把字符串$t$的前$i$个字符全部涂色所需要的最少操作次数。则有$dp[i]=min(dp[j])+1$，要求$t[i,j]$存在某个对应的$s_i$。定义$use[i]$记录$t_i$使用了哪个字符串$s_i$，$from[i]$记录$t_i$是由哪个位置影响的。

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
char t[177], s[17][17];
int lent, lens[17];
int dp[177], use[177], from[177];
bool check(int i, int j) {
    int k = lens[j] - 1;
    while(k >= 0) {
        if(t[i] != s[j][k]) return false;
        i--;
        k--;
    }
    return true;
}
void solve() {
    scanf("%s", t + 1);
    lent = strlen(t + 1);
    for(int i = 1; i <= lent; i++) dp[i] = INF, from[i] = use[i] = -1;

    scanf("%d", &n);
    for(int i = 1; i <= n; i++) {
        scanf("%s", s[i]);
        lens[i] = strlen(s[i]);
    }

    for(int i = 1; i <= lent; i++) {
        int id = -1;
        for(int j = 1; j <= n; j++) {
            if(lens[j] > i) continue;
            if(check(i, j) && dp[i - lens[j]] + 1 < dp[i]) {
                dp[i] = dp[i - lens[j]] + 1;
                id = j;
                use[i] = id;
                from[i] = i;
            }
        }
        if(id == -1) continue;
        for(int k = i; k > i - lens[id]; k--) {
            if(dp[i] < dp[k]) {
                dp[k] = dp[i];
                use[k] = id;
                from[k] = i;
            }
        }
    }

    if(dp[lent] == INF) puts("-1");
    else {
        printf("%d\n", dp[lent]);
        int sId = use[lent];
        int p = lent - lens[sId] + 1;
        while(sId != 0) {
            printf("%d %d\n", sId, p);
            int id = from[p - 1];
            sId = use[id];
            p = id - lens[sId] + 1;
        }
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

## E. Add Modulo 10

**题意**：给定一个数组，每次选择一个元素$a_i$，使得$a_i=a_i+(a_i \mod{10})$。现在问能不能通过操作使数组中的元素全部相等。

**解法**：存在以下循环：

- 0->0
- 1->2->4->8->6->2
- 3->6->2->4->8->6
- 5->0->0
- 7->4->8->6->2->4
- 9->8->6->2->4->8

我们需要特判0和5的情况，然后单独考虑其他情况。对于数组中的任意两个元素$a_i$和$a_j$，我们总能通过操作使得它们的个位相等，即$(a_i \mod{10})=(a_j \mod{10})$。然后对于较小的元素，我们总能通过操作使其增大，但注意每经过一次循环，十位会增加2。那么如果这时$(a_i-a_j) \mod{2}=0$的话，我们就可以通过操作不断增大较小的元素使得两者相等。

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
int check() {
    bool ishave = false, other = false;
    for(int i = 0; i < n; i++) {
        if(a[i] % 10 == 0 || a[i] % 10 == 5) ishave = true;
        else other = true;
    }
    if(other) return !ishave;

    for(int i = 1; i < n; i++) {
        if(a[i] % 10 == 5) {
            if(a[i] != a[i - 1]) return 0;
        } else {
            if(a[i - 1] % 10 == 5) {
                if(a[i] != a[i - 1] + 5) return 0;
            } else {
                if(a[i] != a[i - 1]) return 0;
            }
        }
    }

    return 2;
}
bool cal(int x, int y) {
    int tailx = x % 10, headx = x / 10;
    int taily = y % 10, heady = y / 10;

    if(taily % 2 != 0) {//1 3 7 9
        taily += taily;
        if(taily > 9) {
            taily %= 10;
            heady++;
        }
    }

    while(tailx != taily) {
        tailx += tailx;
        if(tailx > 9) {
            tailx %= 10;
            headx++;
        }
    }

    return (heady - headx) % 2 == 0;
}
void solve() {
    scanf("%d", &n);
    for(int i = 0; i < n; i++) scanf("%d", &a[i]);
    sort(a, a + n);
    int is_0_5 = check();
    if(is_0_5 != 1) {
        puts(is_0_5 == 0 ? "NO" : "YES");
        return;
    }

    for(int i = 0; i < n - 1; i++) {
        if(!cal(a[i], a[n - 1])) {
            puts("NO");
            return;
        }
    }
    puts("YES");
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

## F. Build a Tree and That Is It

**题意**：构造一棵无根树，使其满足顶点1到顶点2的最短路径为d12、顶点2到顶点3的最短路径为d23、顶点3到顶点1的最短路径为d31。

**解法**：![tree](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20220804111800.png)

最终的树总是形如上图所示，建立方程组联立求解即可。

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
int d12, d23, d31;
// x+y=d12
// y+z=d23
// x+z=d31
void solve() {
    scanf("%d%d%d%d", &n, &d12, &d23, &d31);

    int z = d23 + d31 - d12;
    if(z < 0 || z % 2 != 0) {
        puts("NO");
        return;
    }

    z /= 2;
    int x = d31 - z;
    if(x < 0 || x > d12) {
        puts("NO");
        return;
    }

    if(d12 + z + 1 > n) {
        puts("NO");
        return;
    }

    puts("YES");
    int nextId = 4;
    int ans[d12 + 1];
    ans[0] = 1, ans[d12] = 2;
    for(int i = 1; i < d12; i++) {
        if(z == 0 && i == d31) ans[i] = 3;
        else ans[i] = nextId++;
    }
    for(int i = 1; i <= d12; i++) printf("%d %d\n", ans[i - 1], ans[i]);
    if(z > 0) {
        int start = ans[d31 - z];
        for(int i = 0; i < z - 1; i++) {
            printf("%d %d\n", start, nextId);
            start = nextId;
            nextId++;
        }
        printf("%d 3\n", start);
    }
    while(nextId <= n) {
        printf("%d 3\n", nextId);
        nextId++;
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

## G. Path Prefixes

**题意**：给定一棵有根树，每条边有两种权值$a_i$和$b_i$。对于任意顶点$i$，有唯一的从根结点到顶点$i$的路径，路径上权值$a_i$的和为$A_i$，求路径的最大前缀满足前缀上$b_i$的和$B_j$不超过$A_i$。

**解法**：注意$b_i$是正整数，则$B_i$是递增的。递归遍历整棵树，保存路径上的$B_i$集合，可以二分查找满足条件的最大前缀。

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
int p[MAXN];
LL a[MAXN], b[MAXN];
LL prea[MAXN];
LL preb[MAXN], tot = 1;
vector<int> child[MAXN];
int ans[MAXN];
void dfs(int index, int root) {
    prea[index] = prea[root] + a[index];
    preb[tot] = preb[tot - 1] + b[index];
    tot++;
    ans[index] = upper_bound(preb, preb + tot, prea[index]) - preb - 2;

    int len = child[index].size();
    for(int i = 0; i < len; i++) dfs(child[index][i], index);
    tot--;
}
void solve() {
    scanf("%d", &n);
    tot = 1;
    for(int i = 0; i <= n; i++) child[i].clear();
    for(int i = 2; i <= n; i++) {
        scanf("%d%lld%lld", &p[i], &a[i], &b[i]);
        child[p[i]].push_back(i);
    }

    dfs(1, 0);

    for(int i = 2; i <= n; i++) {
        if(i != 2) putchar(32);
        printf("%d", ans[i]);
    }
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

