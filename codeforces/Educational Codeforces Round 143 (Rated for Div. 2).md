**比赛链接**：<https://codeforces.com/contest/1795>

## A. Two Towers

**题意**：给定两个栈，每个栈中有若干红色或蓝色方块。在两个栈之间移动方块，使得每个栈中任意两个相邻方块的颜色都不一样。问给出的情况是否有解？

**解法**： 先把所有的方块移到一个栈中，若相邻方块颜色相同的位置不超过一次则有解。

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
int n, m;
char s[MAXN], t[MAXN];
void solve() {
    scanf("%d%d", &n, &m);
    scanf("%s%s", s, t);

    int cnt = 0;
    for(int i = 1; i < n; i++) {
        if(s[i] == s[i - 1]) cnt++;
    }
    if(t[m - 1] == s[n - 1]) cnt++;
    for(int i = m - 2; i >= 0; i--) {
        if(t[i] == t[i + 1]) cnt++;
    }

    if(cnt <= 1) puts("YES");
    else puts("NO");
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## B. Ideal Point

**题意**：给定若干个区间，问能否删除一些区间使得数字$k$在区间中出现最多次。

**解法**：限制了不超过50，可以直接删除所有不包含数字$k$的区间，然后暴力判断是否出现了最多次。

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
int l, r;
int a[MAXN];
void solve() {
    scanf("%d%d", &n, &k);
    for(int i = 1; i <= 50; i++) a[i] = 0;
    for(int i = 0; i < n; i++) {
        scanf("%d%d", &l, &r);
        if(l <= k && k <= r) {
            for(int j = l; j <= r; j++) a[j]++;
        }
    }

    for(int i = 1; i <= 50; i++) {
        if(i == k) continue;
        if(a[i] == a[k]) {
            puts("NO");
            return;
        }
    }
    puts("YES");
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## C. Tea Tasting

**题意**：给定$n$杯茶，每杯茶$a_i$毫升。给定$n$个人，第$i$个人每次喝茶$b_i$毫升，若茶不足$b_i$毫升则会喝完这杯茶。第$i$杯茶从第$i$个人开始依次往后喝，直到最后一个人或者这杯茶被喝完。问每个人最后喝了多少茶？

**解法**：很容易得到一个$O(n^2)$的做法：枚举每杯茶能被哪些人喝，将贡献记录到对应的人即可。下面进行优化，注意是依次往后喝，可以对$b_i$做一次前缀和记为$sum_i$。然后找到一个最小的$j$满足$sum_j - sum_{i-1} \ge a_i$，转换一下这个不等式得到$sum_j \ge a_i + sum_{i-1}$，通过二分查找可以很快的求得下标$j$。这意味着区间$[i,j]$中的人喝到了第$i$杯茶，其中区间$[i,j-1]$中的人喝了他们上限的茶，最后一个人$j$喝了仅剩的茶。可以维护一个$cnt_i$表示第$i$个人喝了几次他们上限的茶，维护一个$ans_i$表示第$i$个人喝了多少仅剩的茶，注意这里维护的一个是次数一个是总量。那么每个人喝的茶为：$cnt_i \times b_i + ans_i$，最终复杂度为$O(n \log n)$。

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
int n;
LL a[MAXN], b[MAXN];
LL cnt[MAXN], ans[MAXN];
void solve() {
    scanf("%d", &n);
    for(int i = 1; i <= n; i++) scanf("%lld", &a[i]);
    for(int i = 1; i <= n; i++) {
        scanf("%lld", &b[i]);
        b[i] += b[i - 1];
        cnt[i] = ans[i] = 0;
    }

    for(int i = 1; i <= n; i++) {
        ///第i杯茶被区间[i,r]的人喝了
        int r = lower_bound(b + 1, b + 1 + n, a[i] + b[i - 1]) - b;
        cnt[i]++;
        cnt[r]--;
        ans[r] += a[i] - (b[r - 1] - b[i - 1]);
    }

    for(int i = 1; i <= n; i++) {
        cnt[i] += cnt[i - 1];
        printf("%lld ", cnt[i] * (b[i] - b[i - 1]) + ans[i]);
    }
    putchar(10);
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## D. Triangle Coloring

**题意**：给定$n$个顶点三元组，每个三元组中的顶点两两建立有正权值的边。现在把每个顶点染成红色或蓝色，要求每个顶点只有一种颜色且红、蓝色顶点的数量相等。定义染色权重为连接不同颜色顶点的边的权值之和。求有多少种染色方式能获得最大的染色权重。

**解法**：对于每个三元组只有两种染色模式。第一种模式三个顶点染同样的颜色，权重为0。第二种模式其中一个顶点的颜色和另外两个顶点的颜色不同，权重为该顶点边的权值之和。要使染色的权重最大，则每个三元组都应该选择第二种染色模式。

那现在我们可以选出一半的三元组染成红色有两个顶点，另一半的三元组染成蓝色有两个顶点即可满足顶点数量相等的要求。接着让每个三元组的染色方式总是贡献权值最大的两条边，定义$c_i$表示第$i$个三元组的染色方式。

- $c_i = 3$，三条边权值相等。
- $c_i = 2$，两条边权值相等且小于第三边。
- $c_i = 1$，两条边权值相等且大于第三边，三条边权值都不相等。

最终的方案数为：$C_{n}^{n/2} \prod \limits_{i=1}^{n} c_i$

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
LL a[MAXN];
int M = 998244353;
LL fact[MAXN];
LL inv[MAXN];
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
    for(int i = 1; i <= n; i++) {
        fact[i] = fact[i - 1] * i % M;
    }

    inv[n] = fast_power(fact[n], M - 2, M);
    for (int i = n - 1 ; i >= 0; i--) {
        inv[i] = inv[i + 1] * (i + 1) % M;
    }
}
int main() {
    scanf("%d", &n);
    init();
    for(int i = 0; i < n; i++) scanf("%lld", &a[i]);

    LL ans = fact[n / 3] * inv[n / 6] % M * inv[n / 6] % M;
    for(int i = 0; i < n; i += 3) {
        if(a[i] == a[i + 1] && a[i] == a[i + 2]) {
            ans = ans * 3 % M;
            continue;
        }
        if(a[i] == a[i + 1] && a[i] < a[i + 2]) {
            ans = ans * 2 % M;
            continue;
        }
        if(a[i] == a[i + 2] && a[i] < a[i + 1]) {
            ans = ans * 2 % M;
            continue;
        }
        if(a[i + 2] == a[i + 1] && a[i + 2] < a[i]) {
            ans = ans * 2 % M;
            continue;
        }
    }

    printf("%lld\n", ans);
    return 0;
}
```

## E. Explosions?

**题意**：有$n$个怪物，每个怪物的生命值为$h_i$。基础攻击每次消耗1MP，对目标造成1点伤害。魔法攻击消耗 $x$ MP，对目标造成$x$点伤害且如果目标死亡还会造成连锁伤害。连锁伤害：若目标当前有$h_i$点生命值且死于魔法攻击，则该目标对相邻位置的怪物造成$h_i-1$的伤害，连锁伤害会传递。现在先使用若干次基础攻击再使用一次魔法攻击来消灭所有的怪物，求需要消耗的最少MP。

**解法**：

- 造成的连锁伤害总是越多越好，因此最少的MP = 总的生命值 - 最大的连锁伤害。
- 需要使用一次魔法攻击来消灭所有的怪物，则需要先使用基础攻击使魔法攻击的目标怪物$p$左侧是严格递增的、右侧是严格递减的（在忽略首尾的0之后）。
- 考虑左边的部分，对于怪物$p$总是会选择使用$h_p$的魔法攻击，这样恰好击杀并产生连锁伤害。
  - $h_{p-i} \gt h_p-i$，可以预先使用基础攻击使$h{p-i}$降为$h_p-i$满足连锁伤害的条件。
  - $h_{p-i} \le h_p-i$，这时造成的伤害已经跟$p$没有关系了。
- 定义$dp_i$表示消灭第$i$只怪物左边能产生的连锁伤害的最大值，则有$dp_i = dp_j + sum$，其中$j$是最右边无法满足$h_j$恰好递减1的下标，$sum$是可以连续递减部分的总和。想要快速的求出下标$j$，单调栈是非常适合的数据结构。
- 右边和左边是对称的处理方式。

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
int n;
int h[MAXN];
LL dpL[MAXN], dpR[MAXN];
struct node {
    int id;
    int val;
};
void solveLeft() {
    dpL[0] = 0;
    stack<node> st;
    for(int i = 1; i <= n; i++) {
        node now = node {i, h[i] - i};
        while(!st.empty() && st.top().val > now.val) st.pop();

        int l = max(0, i - h[i]);//l是不能直接由i影响的最大下标
        if(!st.empty()) l = max(l, st.top().id);
        LL len = i - l;//等差下降的长度
        dpL[i] = h[i] * len - len * (len - 1) / 2;//等差下降部分的贡献
        if(l >= 1 && len < h[i]) dpL[i] += dpL[l];//还能往左走

        st.push(now);
    }
}
void solveRight() {
    dpR[n + 1] = 0;
    stack<node> st;
    for(int i = n; i > 0; i--) {
        node now = node {i, h[i] - (n - i + 1)};
        while(!st.empty() && st.top().val > now.val) st.pop();

        int r = min(n + 1, i + h[i]);
        if(!st.empty()) r = min(r, st.top().id);
        LL len = r - i;
        dpR[i] =  h[i] * len - len * (len - 1) / 2;
        if(r <= n && len < h[i]) dpR[i] += dpR[r];

        st.push(now);
    }
}
void solve() {
    scanf("%d", &n);
    LL sum = 0;
    for(int i = 1; i <= n; i++) {
        scanf("%d", &h[i]);
        sum += h[i];
    }

    solveLeft();
    solveRight();

    LL ans = (LL) INF * INF;
    for(int i = 1; i <= n; i++) {
        ans = min(ans, sum - dpL[i] - dpR[i] + h[i] * 2);
    }
    printf("%lld\n", ans);
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## F. Blocking Chips

**题意**：给定一棵大小为 $1 \le n \le 2 \cdot 10^5$ 的树和若干个起点，每个起点依次进行移动，访问过的结点不能再次访问，问总共最多能移动多少次？

**解法**：可以移动的步数具有单调性，可以二分求解能移动的最大的总步数。给定总步数，可以求得每个起点需要移动的步数。随机选定根结点，从层级较深的起点开始考虑。如果往子结点移动可以满足条件则优先往子结点移动，否则移动一步到父结点，问题转移到父结点上。对于每个结点，往子结点移动是否可以满足条件，进行一次`DFS`预处理即可。

优先队列写法，较复杂

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
int u, v;
struct node {
    int id;
    int root;//根结点id
    int level;//层级
    bool colored;//是否已染色
    vector<int> edge;//边
    int aim;//需要移动的步数
    int maxlen;//可访问的孩子的最长链
    bool lazy;//标记maxlen是否已失效

    bool operator <(const node& a)const {
        return level < a.level;
    }
} tree[MAXN];

void dfs(int id, int root) {
    tree[id].root = root;
    tree[id].level = tree[root].level + 1;

    tree[id].maxlen = 0;
    int sonSize = tree[id].edge.size();
    for(int i = 0; i < sonSize; i++) {
        int sonId = tree[id].edge[i];
        if(sonId == root) continue;
        if(tree[sonId].lazy) dfs(sonId, id);
        if(!tree[sonId].colored) { //初始化maxlen
            tree[id].maxlen = max(tree[id].maxlen, tree[sonId].maxlen + 1);
        }
    }
    tree[id].lazy = false;
}
void initTree(int step) {
    tree[0].colored = true;
    for(int i = 1; i <= n; i++) {
        tree[i].id = i;
        tree[i].level = 0;
        tree[i].colored = false;
        tree[i].aim = 0;
        tree[i].lazy = true;
    }
    for(int i = 1; i <= k; i++) {
        tree[a[i]].colored = true;
        tree[a[i]].aim = step / k;
        if(step % k >= i) tree[a[i]].aim++;
    }
    dfs(1, 0);
}

struct qnode {
    int id, level;

    bool operator <(const qnode& a)const {
        if(level != a.level)return level < a.level;
        return id > a.id;
    }
};
priority_queue<qnode> pq;
void up(int id) {
    while(!tree[id].lazy) {
        tree[id].lazy = true;
        id = tree[id].root;
    }
}
void gotoRoot(int nowId) {
    int rootId = tree[nowId].root;
    tree[rootId].colored = true;
    tree[rootId].aim = tree[nowId].aim - 1;
    up(rootId);
}
bool check(int step) {
    initTree(step);
    while(!pq.empty()) pq.pop();
    for(int i = 1; i <= k; i++) pq.push(qnode {a[i], tree[a[i]].level});
    while(!pq.empty()) {
        int nowId = pq.top().id;
        pq.pop();
        if(tree[nowId].lazy) dfs(nowId, tree[nowId].root);//当前结点需要更新
        if(tree[nowId].maxlen >= tree[nowId].aim) continue;//当前点可以向下走

        int rootId = tree[nowId].root;
        if(tree[rootId].colored) return false;//父结点已经被访问了
        gotoRoot(nowId);//往父结点走一步
        pq.push(qnode {rootId, tree[rootId].level});
    }
    return true;
}
int erfen() {
    int l = 0, r = n;
    while(l <= r) {
        int mid = (l + r) / 2;
        if(check(mid)) l = mid + 1;
        else r = mid - 1;
    }
    return r;
}
void solve() {
    scanf("%d", &n);
    for(int i = 0; i <= n; i++) tree[i].edge.clear();
    for(int i = 1; i < n; i++) {
        scanf("%d%d", &u, &v);
        tree[u].edge.push_back(v);
        tree[v].edge.push_back(u);
    }

    scanf("%d", &k);
    for(int i = 1; i <= k; i++) scanf("%d", &a[i]);

    printf("%d\n", erfen());
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

递归写法

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
vector<int> edge[MAXN];

int req[MAXN];//需要移动的步数
bool use[MAXN];//是否被使用
int maxlen[MAXN];//子结点最远距离
bool dfs(int id, int root) {
    int sonSize = edge[id].size();
    for(int i = 0; i < sonSize; i++) {
        int sonId = edge[id][i];
        if(sonId == root) continue;
        if(!dfs(sonId, id)) return false;//子结点无法满足条件
        if(!use[sonId]) maxlen[id] = max(maxlen[id], maxlen[sonId] + 1);
    }

    if(maxlen[id] >= req[id]) return true;//可以往子结点走
    if(root == 0 || use[root]) return false;//父结点已经被访问了
    use[root] = true;//往父结点走
    req[root] = req[id] - 1;
    return true;
}
bool check(int step) {
    for(int i = 0; i <= n; i++) {
        use[i] = false;
        req[i] = maxlen[i] = 0;
    }
    for(int i = 1; i <= k; i++) {
        req[a[i]] = step / k;
        if(step % k >= i) req[a[i]]++;
        use[a[i]] = true;
    }
    return dfs(1, 0);
}
int erfen() {
    int l = 0, r = n;
    while(l <= r) {
        int mid = (l + r) / 2;
        if(check(mid)) l = mid + 1;
        else r = mid - 1;
    }
    return r;
}
void solve() {
    scanf("%d", &n);
    for(int i = 0; i <= n; i++) edge[i].clear();
    int u, v;
    for(int i = 1; i < n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v);
        edge[v].push_back(u);
    }

    scanf("%d", &k);
    for(int i = 1; i <= k; i++) scanf("%d", &a[i]);

    printf("%d\n", erfen());
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## G. Removal Sequences

**题意**：给定一个简单无向图，不包含自环和重边。每个结点具有点权，只有当结点的点权等于结点的度数时才可以删除该结点。删除一个结点也会删除该结点的边。保证至少存在一个合法的结点序列可以删除所有的结点。如果在两个不同的结点序列中，结点 $x$ 与结点 $y$ 的相对位置不同，则称这样的结点对是好的。求给定的无向图中有多少好的结点对？

**解法**：

- 保证至少存在一个合法的序列，那么最初必然存在这样的一个结点集合 $s$ 。集合 $s$ 中结点的权值都等于结点的度数，也就是不用做任何操作就满足可删除的条件。且集合中任意两个结点不相邻，因为任意结点相邻在删除一个结点后都将导致另一个结点无法删除。
- 在删除集合中的一个或多个结点后可能会导致某个结点变得可删除，换句话说：有些结点要能删除必须先删除某些特定的结点。从这里可以看出，虽然是无向图，但是删除的顺序并不是那么无向。
- 建立有向图，如果结点 $u$ 从结点 $v$ 出发可达，则表示要删除结点 $u$ ，必须先删除结点 $v$ 。可以广搜一遍无向图来建立该有向图。
- 有向图中结点 $u$ 和结点 $v$ 不可达，则该顶点对是好的。问题到这转换成了无环有向图中可达顶点对的数量。
- 一个朴素的方案是为每个结点维护一个`bitset`，表示从哪些结点出发可到达当前结点，广搜来传播更新标志位。这个方案时间复杂度是$O(n + m)$的，问题在于$10^{10}$个标志位需要1G的内存。一个有趣的优化是这样的，分批计算。每一次只计算64个结点从哪些结点是可达的，优化后的时间复杂度是$O(\frac{n}{64} \times (n + m))$的，但内存问题得到了解决，换而言之这是一个时间换空间的方案。

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
int n, m;
int a[MAXN];

int u, v;
vector<vector<int>> node;

bool visit[MAXN];
int degree[MAXN];
queue<int> q;
vector<pair<int, int>> edge;//有向边
unsigned LL mask[MAXN];

void init() {
    while(!q.empty()) {
        q.pop();
    }
    for(int i = 0; i < n; i++) {
        mask[i] = 0;
        visit[i] = false;
        degree[i] = node[i].size();
        if(degree[i] == a[i]) q.push(i);
    }
    edge.clear();
}
void solve() {
    scanf("%d%d", &n, &m);
    node.clear();
    node.resize(n);
    for(int i = 0; i < n; i++) scanf("%d", &a[i]);
    for(int i = 0; i < m; i++) {
        scanf("%d%d", &u, &v);
        --u;
        --v;
        node[u].push_back(v);
        node[v].push_back(u);
    }

    init();
    while(!q.empty()) {//建立有向图
        int u = q.front();
        q.pop();
        visit[u] = true;
        for(int v : node[u]) {
            if(visit[v]) continue;
            --degree[v];
            edge.push_back({u, v});
            if(degree[v] == a[v]) q.push(v);
        }
    }

    LL ans = n * 1LL * (n + 1) / 2;//总的顶点对数
    //mask[i] 表示在这一轮循环中，能到达第 i 个顶点的顶点数
    for(int l = 0; l < n; l += 64) {
        int r = min(n, l + 64);
        for(int i = l; i < r; i++) mask[i] = ((unsigned LL) 1) << (i - l);
        for(int i = edge.size() - 1; i >= 0; i--) {//逆序遍历
            mask[edge[i].first] |= mask[edge[i].second];
        }
        for(int i = 0; i < n; i++) {
            ans -= __builtin_popcountll(mask[i]);
            mask[i] = 0;
        }
    }

    printf("%lld\n", ans);
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```