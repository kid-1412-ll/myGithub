**比赛链接**：<http://codeforces.com/contest/1625>

## A. Ancient Civilization

**题意**：定义两个数之间的距离为这两个数二进制下不同位的数量，现给出$n$个数，求出到这$n$个数距离之和最小的数。

**解法**：每个位可以单独求解，若$n$个数中该位为1的数量大于为0的数量则将该位设置为1，否则设置为0。

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
int x;
int cnt[117];
int main() {
    scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &k);
        memset(cnt, 0, sizeof(cnt));
        for(int i = 0; i < n; i++) {
            scanf("%d", &x);
            for(int j = 0; j < k; j++) {
                cnt[j] += x % 2;
                x >>= 1;
            }
        }
        int sum = 0, num = 1;
        for(int i = 0; i < k; i++) {
            if(cnt[i] > n - cnt[i]) sum += num;
            num <<= 1;
        }
        printf("%d\n", sum);
    }
    return 0;
}
```

## B. Elementary Particles

**题意**：给定一个字符串，选择两个不同的子串，要求至少存在一个相同位置的元素相同，求子串的最长长度。

**解法**：设$s[u]=s[v]$，则右边的子串可向右拓展到字符串末尾、左边的子串可向左拓展到字符串的起始，子串长度为$n-(v-u)$，因此$v-u$越小则子串长度越长。

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
int a[MAXN];
int fi[MAXN], se[MAXN];


int main() {
    scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        int ans = -1;
        for(int i = 0; i <= 150077; i++) fi[i] = se[i] = -1;
        for(int i = 1; i <= n; i++) {
            scanf("%d", &a[i]);
            if(fi[a[i]] == -1) fi[a[i]] = i;
            else if(se[a[i]] == -1) {
                se[a[i]] = i;
            } else {
                fi[a[i]] = se[a[i]];
                se[a[i]] = i;
            }
            if(fi[a[i]] != -1 && se[a[i]] != -1)
                ans = max(ans, n - (se[a[i]] - fi[a[i]]));
        }
        printf("%d\n", ans);
    }
    return 0;
}
/*
9
2 1 2 1 2 2 1 2 1
9
3 1 4 1 5 6 1 7 1
*/
/*
8
7
*/
```

## C. Road Optimization

**题意**：一条道路被分为$n$段，给定每段道路的长度$d[i]$和区间限速$a[i]$，表示该段道路内每公里所需要的时间。现在可以删除最多$k$个区间限速标志，被删除区间限速将是上一个未被删除的限速标志。起点的限速标志不能被删除，终点没有限速标志，求走完整段道路所需的最少时间。

**解法**：

​	很容易想到定义$dp[i][j]$表示前$i$段道路删除了$j$个限速标志后通过所需要的最少时间，有状态转移方程为$dp[i][j]=min(dp[i-1][j-1]+speed[i-1][j-1]*len,dp[i-1][j]+speed[i-1][j]*len)$。但这个方程是错误的，因为当$dp[i-1][j-1]$增大时$speed[i-1][j-1]$可能减小，而总时间$dp[i-1][j-1]+speed[i-1][j-1]*len$减少。例如hack样式：`3 100 1，0 5 10，1 2 3`。

​	要使$speed[i-1][j-1]$减小，显然只能连续的删除前面某一段的限速标志。重新定义$dp[i][j]$表示前$i-1$段道路删除了$j$个限速标志后到达位置$i$所需要的最少时间。则有状态转移方程$dp[i][j]=min(dp[pos][j-(i-pos-1)]+a[pos]*len)$，$pos$表示可以往前连续删除限速标志的合法位置。

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

int n, l, k;
int d[577], a[577];
int dp[577][577];

int main() {
    scanf("%d%d%d", &n, &l, &k);
    for(int i = 1; i <= n; i++) cin >> d[i];
    for(int i = 1; i <= n; i++) cin >> a[i];
    d[++n] = l;
    a[n] = INF;

    for(int i = 1; i <= n; i++)
        for(int j = 0; j <= k; j++)
            dp[i][j] = INF;
    dp[1][0] = 0;

    for(int i = 2; i <= n; i++) {
        for(int j = 0; j <= k; j++) {
            if(j >= i) break;
            for(int pos = i - 1; pos >= 1; pos--) {
                int pk = j - (i - pos - 1);
                if(pk < 0) break;
                int len = d[i] - d[pos];
                dp[i][j] = min(dp[i][j], dp[pos][pk] + a[pos] * len);
            }
        }
    }

    int ans = dp[n][0];
    for(int i = 0; i <= k; i++) ans = min(ans, dp[n][i]);
    cout << ans << endl;
    return 0;
}
/**
5 10 2
0 2 4 6 8
5 4 3 2 1

30

5 10 2
0 2 4 6 8
5 4 1 2 3

24

3 100 1
0 5 10
1 2 3

195
*/
```

## D. Binary Spiders

**题意**：给定$n(2 \leq n \leq 3 \cdot 10^5)$个非负整数$(0 \leq a_i \leq 2^{30}-1)$的数组，求一个尽可能大的子集满足集合中任意两个数亦或的值不小于$k(0 \leq k \leq 2^{30}-1)$。

**解法**：首先存在结论：将集合有序排列之后，集合任意两个数亦或的最小值一定出现在相邻两数之间。易证：若$a<b<c$，则有$min(a \bigoplus b , b \bigoplus c) \leq (a \bigoplus c)$。

​	数组有序排列后求最大子集，设$dp[i]$表示以第$i$个数结尾的最大子集大小，则有$dp[i]=max(dp[j])+1，j<i且a_i \bigoplus a_j \geq k$。

​	现在问题变成如何快速的求出$max(dp[j])$，可以维护一棵trie，每个结点记录子树下的最大值，再根据亦或运算的性质可以在$\ln{a_i}$的复杂度下遍历`trie`得到结果。

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

int n, k;
struct Data {
    int id;
    int val;

    bool operator <(const Data &a) const {
        if(val != a.val)return val < a.val;
        return id < a.id;
    }
} ans;
Data a[MAXN];
int pre[MAXN];
struct Node {
    Data data;
    int l, r;
};
int tot = 0;
Node tree[MAXN * 10];
//目前在root结点，将要去第level层的结点，当前结点的值为ybit
Data query(int x, int root, int level, int ybit) {
    if(root == 0 && level != 29) { //不存在的结点
        return Data {0, 0};
    }
    if(level == -1) {//到第0层了
        if(((x & 1) ^ ybit) >= (k & 1)) return tree[root].data;
        else return Data {0, 0};
    }

    int kbit = (k >> level) & 1;
    int xbit = (x >> level) & 1;
    if(kbit == 0) {
        if(xbit == 0) {
            Data zero = query(x, tree[root].l, level - 1, 0);
            Data one = tree[tree[root].r].data;
            return max(zero, one);
        } else {
            Data zero = tree[tree[root].l].data;
            Data one = query(x, tree[root].r, level - 1, 1);
            return max(zero, one);
        }
    } else {
        if(xbit == 0) {
            return query(x, tree[root].r, level - 1, 1);
        } else {
            return query(x, tree[root].l, level - 1, 0);
        }
    }
}
int getDown(int root, int to) {
    if(to == 0) {
        if(tree[root].l == 0) tree[root].l = ++tot;
        return tree[root].l;
    } else {
        if(tree[root].r == 0) tree[root].r = ++tot;
        return tree[root].r;
    }
}
void add(int x, Data data) {
    for(int level = 29, index = 0; level >= 0; level--) {
        //先到下一层
        int xbit = (x >> level) & 1;
        if(xbit == 0) index = getDown(index, 0);
        else  index = getDown(index, 1);
        //更新level层的值
        tree[index].data = max(tree[index].data, data);
    }
}
int main() {
    scanf("%d%d", &n, &k);
    for(int i = 1; i <= n; i++) {
        scanf("%d", &a[i].val);
        a[i].id = i;
    }
    sort(a + 1, a + 1 + n);

    for(int i = 1; i <= n; i++) {
        Data num = query(a[i].val, 0, 29, 0);
        pre[i] = num.id;

        Data now = Data {i, num.val + 1};
        ans = max(ans, now);
        add(a[i].val, now);
    }

    if(ans.val < 2) puts("-1");
    else {
        printf("%d\n", ans.val);
        int index = ans.id;
        while(index) {
            printf("%d ", a[index].id);
            index = pre[index];
        }
    }
    return 0;
}
/**
20 15
2 14 12 10 4
14 14 4 0 14
0 8 0 2 0
0 14 6 12 2

2 15
8 6

6 8
2 8 4 16 10 14

6 1024
1 2 3 1 4 0
*/
```

## E1. Cats on the Upgrade (easy version)

**题意**：给定一个长度为$n(2 \leq n \leq 3 \cdot 10^5)$的括号序列，$q(1 \leq q \leq 3 \cdot 10^5)$次询问，每次询问给出一个区间$[l,r]$，保证区间内的括号是匹配的。问该区间内有多少个子区间的括号是匹配的。

**解法**：首先对括号序列建树，当遇到$'('$时往下一层建立新的结点，当遇到匹配的$')'$时往上一层。

![建树](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20220707095402.png)

​	然后设$dp[i]$表示区间$[1,i]$内括号匹配子区间的数量，$cnt[i]$表示以$s[i]$结尾括号对的父结点在区间$[1,i]$内直接子结点的数量，则有：

- $dp[i]=dp[i-1]$，如果$s[i]$是多余的括号。
- $dp[i]=dp[i-1]+cnt[i]$，如果$s[i]$能配对括号对。

​	那么每次询问$ans[l,r]=dp[r]-dp[l-1]-cnt[l-1]*(cnt[r]-cnt[l-1])$。

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

int n, q;
int op, l, r;
char s[MAXN];
LL cnt[MAXN];//前i个中 和以i结尾的括号对相同父结点的数目
LL sum[MAXN];//前i个中 匹配的子区间数目
int treeL[MAXN], index;//树
int main() {
    scanf("%d%d", &n, &q);
    scanf("%s", s + 1);
    for(int i = 1; i <= n; i++) {
        if(s[i] == '(') treeL[++index] = i;//往下一层
        else if(index > 0) cnt[i] = cnt[treeL[index--] - 1] + 1;
        sum[i] = sum[i - 1] + cnt[i];
    }
    while(q--) {
        scanf("%d%d%d", &op, &l, &r);
        printf("%lld\n", sum[r] - sum[l - 1] - cnt[l - 1] * (cnt[r] - cnt[l - 1]));
    }
    return 0;
}
```

## E2. Cats on the Upgrade (hard version)

**题意**：给定一个长度为$n(2 \leq n \leq 3 \cdot 10^5)$的括号序列，$q(1 \leq q \leq 3 \cdot 10^5)$次删除或询问操作。每次删除给出一个区间$[l,r]$，保证$s[l]='(',s[r]=')'$，其他位置均为$'.'$。每次询问给出一个区间$[l,r]$，保证区间内的括号是匹配的。问该区间内有多少个子区间的括号是匹配的。

**解法**：首先对括号序列建树，当遇到$'('$时往下一层建立新的结点，当遇到匹配的$')'$时往上一层。

![建树](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20220707095402.png)

​	与easy version版本的问题不同，考虑到有删除操作，hard version版本不能直接以前缀和的方式存储前$i$项中括号匹配子区间的数量。

​	可以定义$tree[i]$存储当前结点下一层级能组成的子区间数量，则$tree[i]=\frac{son[i] \times (son[i]+1)}{2}$，例如$tree[1]=\frac{5 \times 6}{2}=15$。

​	先考虑查询操作，任意区间内括号匹配子区间的数量$ans[l,r]$，定义$id_i$表示$s[i]$对应树中的结点编号、$cnt[l,r]$表示区间$[l,r]$内顶层结点的数量。则$ans[l,r] = \sum_{i=id_l}^{id_r+size_r}{tree[i]} + \frac{cnt[l,r] \times (cnt[l,r] + 1)}{2}$，例如$ans[8,15] = \sum_{i=5}^{7+1}{tree[i]} +\frac{cnt[8,15] \times (cnt[8,15]+1)}{2} = 5$。考虑$\sum_{i=id_l}^{id_r+size_r}{tree[i]}$怎么计算，可以对$tree[i]$使用树状数组进行查询。

​	现在来考虑删除操作，根据题目限制每次删除的都是叶子结点，删除一个叶子结点$i$则父结点$tree[fa_i]$会减小$son[fa_i]$，也就是说$son[i]$是需要动态维护的。既然$son[i]$是动态的，那么$cnt[l,r]$也需要动态维护，可以对每个树结点建立树状数组来维护某个子结点是否被删除，这样可以在$log$的时间复杂度下维护$cnt[l,r]$。

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

int n, q;
int op, l, r;
char s[MAXN];
//括号树
int tot = 1, now = 1;
int s_tree[MAXN];//s[i]对应树中的第几个结点
struct Node {
    int fa;//父结点
    int broRank;//在兄弟结点中排第几个
    int son;//直接子结点的数量
    int tree_size;//子树大小
} tree[MAXN];
//树状数组
int lowbit(int x) {
    return x & -x;
}
struct FenwickTree {
    int length;
    vector<LL> sum;
    void init(int x) {
        length = x;
        sum.resize(length);
    }
    void update(int p, LL x) {
        while(p < length) {
            sum[p] += x;
            p += lowbit(p);
        }
    }
    LL query(int p) {
        LL ans = 0;
        while(p) {
            ans += sum[p];
            p -= lowbit(p);
        }
        return ans;
    }
} total, part[MAXN];//总的树状数组(子区间的数量)、每个树结点的树状数组(被删除结点的数量)
int main() {
    scanf("%d%d%s", &n, &q, s + 1);
    for(int i = 1; i <= n; i++) {
        if(s[i] == '(') {
            tree[now].son++;
            tot++;
            tree[tot].fa = now;
            tree[tot].broRank = tree[now].son;
            s_tree[i] = tot;
            now = tot;
        } else if(now > 1) {
            s_tree[i] = now;
            now = tree[now].fa;
        }
    }

    total.init(tot + 1);
    for(int i = 1; i <= tot; i++) {
        tree[i].tree_size = 1;
        part[i].init(tree[i].son + 1);
        total.update(i, (LL)tree[i].son * (tree[i].son + 1) / 2);
    }
    for(int i = tot; i > 1; i--)
        tree[tree[i].fa].tree_size += tree[i].tree_size;

    while(q--) {
        scanf("%d%d%d", &op, &l, &r);
        int fa = tree[s_tree[l]].fa;
        int idL = s_tree[l], idR = s_tree[r];
        if(op == 1) {
            total.update(fa, -tree[fa].son);
            tree[fa].son--;
            part[fa].update(tree[idL].broRank, 1);
        } else {
            LL ans =  total.query(idR + tree[idR].tree_size - 1) - total.query(idL - 1);
            LL num = part[fa].query(tree[idR].broRank) - part[fa].query(tree[idL].broRank - 1);
            LL cnt = tree[idR].broRank - tree[idL].broRank + 1 - num;
            printf("%lld\n", ans + cnt * (cnt + 1) / 2);
        }
    }
    return 0;
}
```
