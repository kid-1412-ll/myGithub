**比赛链接**：<https://codeforces.com/contest/1800>

## A. Is It a Cat?

**题意**：给定一个字符串，在不区分大小写的情况下是否形如`m+e+o+w+`，+号表示至少1个。

**解法**：模拟就好，但小心形如`eeow`的情况。

**Tips**：第一次被hack，这很有趣，不是吗？T_T！

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
char s[MAXN];
void solve() {
    scanf("%d", &n);
    scanf("%s", s);
    for(int i = 0; i < n; i++) s[i] = tolower(s[i]);

    string t = "";
    t.push_back(s[0]);
    for(int i = 1; i < n; i++)
        if(s[i] != s[i - 1]) t.push_back(s[i]);

    puts(t == "meow" ? "YES" : "NO");
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

**题解代码：**`transform()、tolower()、erase()、unique()`方法的灵活使用，实在是太优雅了。

```cpp
#include <bits/stdc++.h>
using namespace std;

void solve(){
    int n;
    cin >> n;
    string s;
    cin >> s;
    transform(s.begin(), s.end(), s.begin(), [] (char c) {
        return tolower(c);
    });
    s.erase(unique(s.begin(), s.end()), s.end());
    cout << (s == "meow" ? "YES" : "NO") << "\n";
}

int main(){
    int t;
    cin >> t;
    while(t--) solve();
}
```

## B. Count the Number of Pairs

**题意**：给定一个只包含大小写字母的字符串，最多执行 $k$ 次操作，每次操作转换一个字符的大小写。求最多可以匹配多少对大小写的字符对。

**解法**：统计每种字符出现的次数，贪心的去匹配就好了。

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
char s[MAXN];
int a[2][30];
void solve() {
    scanf("%d%d", &n, &k);
    scanf("%s", s);

    for(int i = 0; i < 30; i++) a[0][i] = a[1][i] = 0;
    for(int i = 0; i < n; i++) {
        if('a' <= s[i] && s[i] <= 'z') a[0][s[i] - 'a']++;
        else a[1][s[i] - 'A']++;
    }

    int ans = 0;
    for(int i = 0; i < 26; i++) {
        ans += min(a[0][i], a[1][i]);
        int x = abs(a[0][i] - a[1][i]) / 2;
        ans += min(k, x);
        k -= min(k, x);
    }
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

## C1. Powering the Hero (easy version)

**题意**：给定一个牌组，有两种类型的卡。

- 英雄卡，值始终为0
- 奖励卡，值始终为正整数

每次从牌堆顶拿一张牌，如果是一张奖励卡可以选择放入自己的牌堆或者丢弃，如果是一张英雄卡则使用自己牌堆顶部的奖励卡给英雄附加力量值，被使用过的奖励卡将会被丢弃。问所有英雄的力量值总和最大可以是多少？

**解法**：注意，只需要求出最大的值就可以了，并不需要给出具体的操作方案。一个有意思的事情是，如果每次都选择将奖励卡放入牌堆，当遇到英雄卡的时候理论上最优应该使用哪张奖励卡呢？答案是当前牌堆中值最大的奖励卡，虽然值最大的奖励卡并不一定在牌堆顶部，但是总存在一种操作方式可以使得值最大的奖励卡被选中。换而言之，每次选择牌堆中至最大的奖励卡而不是牌堆顶始终有解。

- 对于C1的数据，每次循环取最大值即可满足条件。
- 对于C2的数据，可以使用优先队列来优化。

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
int s[MAXN];
void solve() {
    scanf("%d", &n);
    for(int i = 1; i <= n; i++) scanf("%d", &s[i]);

    LL ans = 0;
    priority_queue<LL> pq;
    for(int i = 1; i <= n; i++) {
        if(s[i] == 0) {
            if(pq.size() > 0) {
                ans += pq.top();
                pq.pop();
            }
        } else {
            pq.push(s[i]);
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

## C2. Powering the Hero (hard version)

见C1。

## D. Remove Two Letters

**题意**：给定一个字符串，删除相邻的两个字符问能得到多少种不同的子串。

**解法**：考虑删除 $s_i、s_{i+1}$ 和 $s_{i+1}、s_{i+2}$ 两种情况，可以发现对于两种情况的子串下标小于 $i$ 和下标大于 $i+2$ 的字符都会被保留，只要 $s_i=s_{i+2}$ 则两个子串会相同。因此可以用总的子串数减去相同子串的数量来计算不同的子串数量。

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
char s[MAXN];
void solve() {
    scanf("%d%s", &n, s);
    int cnt = 0;
    for(int i = 0; i < n - 2; i++)
        if(s[i] == s[i + 2]) cnt++;
    printf("%d\n", n - 1 - cnt);
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## E1. Unforgivable Curse (easy version)

**题意**：给定两个长度为 $n$ 的字符串 $s$ 和 $t$，每次操作可以交换距离为 $k$ 或者 $k+1$ 的字符，问能否将字符串 $s$ 变成 $t$。在简单版本中 $k$ 始终为3。

**解法**：分情况找一下规律

- 当 $n \lt 5$ 时，1和4可以相互交换，2和3不能进行任何交换操作。
- 当 $n = 5$ 时，1、2、4、5可以相互交换，3不能进行任何交换操作。
- 当 $6 \leq n$ 时，任意位置的两个字符都可以通过操作进行交换。

先特判不能进行交换操作的字符是否相同，再判断可以进行交换的集合中，字符串 $s$ 和 $t$ 的每种字符的数量是否相等，如果相等则总能将字符串 $s$ 变成 $t$。

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
char s[MAXN], t[MAXN];
void solve() {
    scanf("%d%d", &n, &k);
    scanf("%s%s", s + 1, t + 1);

    int cnt[2][30];
    memset(cnt, 0, sizeof(cnt));
    for(int i = 1; i <= n; i++) {
        if(i == 2 && n < 5) {//小于5的时候2需要特判
            if(s[i] != t[i]) {
                puts("NO");
                return;
            }
            continue;
        }
        if(i == 3 && n < 6) {//小于6的时候3需要特判
            if(s[i] != t[i]) {
                puts("NO");
                return;
            }
            continue;
        }
        cnt[0][s[i] - 'a']++;
        cnt[1][t[i] - 'a']++;
    }

    for(int i = 0; i < 26; i++) {
        if(cnt[0][i] != cnt[1][i]) {
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

## E2. Unforgivable Curse (hard version)

**题意**：给定两个长度为 $n$ 的字符串 $s$ 和 $t$，每次操作可以交换距离为 $k$ 或者 $k+1$ 的字符，问能否将字符串 $s$ 变成 $t$。

**解法**：分情况讨论

- 当 $n \leq k$ 时，没有任何字符可以进行交换操作。
- 当 $k \lt n \leq k \times 2$ 时，在区间 $[1,n-k]$ 和 $[k+1,n]$ 区间里的字符总是能够进行交换。
- 当 $k \times 2 \leq n$ 时，任意位置的两个字符都可以通过操作进行交换。

具体的处理方式和简单版本一样。

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
char s[MAXN], t[MAXN];
void solve() {
    scanf("%d%d", &n, &k);
    scanf("%s%s", s + 1, t + 1);

    if(n <= k) {
        puts(strcmp(s + 1, t + 1) == 0 ? "YES" : "NO");
        return;
    }

    int cnt[2][30];
    memset(cnt, 0, sizeof(cnt));
    for(int i = 1; i <= n; i++) {
        if(n < 2 * k) {
            if(n - k + 1 <= i && i <= k && s[i] != t[i]) {
                puts("NO");
                return;
            }
        }
        cnt[0][s[i] - 'a']++;
        cnt[1][t[i] - 'a']++;
    }

    for(int i = 0; i < 26; i++) {
        if(cnt[0][i] != cnt[1][i]) {
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

## F. Dasha and Nightmares

**题意**：给定 $n(1 \le n \le 2 \cdot 10^5)$ 个字符串，拼接两个字符串形成新字符串，要求形成的字符串满足下面的条件，问有多少种不同的拼接方式。

- 字符串长度是奇数
- 字符串恰好包含25种字符
- 字符串中每种字符出现的次数为奇数

**解法**：当满足条件2和条件3的时候，条件1自动满足，因为奇数个奇数的和是奇数。只需要考虑每种字符在每个字符串中是否出现以及出现次数的奇偶性即可，不需要考虑具体的次数，因此使用位掩码来存储每个字符串的特征是一个较好的方案。定义  $a_i$ 表示第 $i$ 个字符串中每种字符是否出现，定义  $b_i$ 表示第 $i$ 个字符串中每种字符出现次数的奇偶性，定义 $f(x)$ 表示 $x$ 中二进制为1的位数。当且仅当 $f(a_i|a_j) = f(b_i \oplus b_j) = 25$ 时的拼接方式满足条件。对于一个确定的字符 $k$，该字符不会在字符串中出现。先排除掉所有包含字符 $k$ 的字符串，当满足条件 $f(b_i\oplus b_j) = 25$ 时，条件 $f(a_i| a_j) = 25$ 也会被满足。

换而言之，先枚举每一种不出现的字符 $k$，排除掉包含字符 $k$ 的字符串，再枚举字符串 $s_i$，寻找满足 $b_j = b_i \oplus (2^{26}-1)$ 的字符串 $s_j$。排序后二分查找或者使用数据结构可以快速查找，总的时间复杂度为 $O(\sum |s| + 26 \cdot n \cdot \log n)$。

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
string s[MAXN];
int a[MAXN], b[MAXN], t[MAXN];
void init() {
    for(int i = 0; i < n; i++) {
        for(char c : s[i]) {
            a[i] |= 1 << (c - 'a');//是否包含该字母
            b[i] ^= 1 << (c - 'a');//字母出现次数的奇偶性
        }
    }
}
LL cal(int x) {
    int k = 0;
    for(int i = 0; i < n; i++) {
        if((a[i] & (1 << x)) == 0) t[k++] = b[i];
    }
    sort(t, t + k);

    LL sum = 0;
    for(int i = 0; i < n; i++) {
        if((a[i] & (1 << x)) == 0) {
            int mask = ((1 << 26) - 1) ^ (1 << x) ^ b[i];
            int l = lower_bound(t, t + k, mask) - t;
            int r = upper_bound(t, t + k, mask) - t;
            sum += r - l;
        }
    }
    return sum;
}
void solve() {
    cin >> n;
    for(int i = 0; i < n; i++) cin >> s[i];
    init();

    LL ans = 0;
    for(int i = 0; i < 26; i++) {
        ans += cal(i);
    }
    cout << ans / 2 << "\n";
}
int main() {
    //scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## G. Symmetree

**题意**：判断一棵给定的树是不是对称的。

**解法**：参考文章[树哈希](https://oi-wiki.org/graph/tree-hash/)

- 如果根结点的所有直接子树都出现了偶数次，则这棵树是对称的。
- 如果根结点的所有直接子树只有一种子树出现了奇数次且该子树是对称的，则这棵树是对称的。
- 否则这棵树不是对称的。

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

typedef unsigned long long ull;
const ull mask = std::chrono::steady_clock::now().time_since_epoch().count();
ull h[MAXN];//子树的hash值
map<ull, bool> symmetrical;//对应hash值的子树是否对称
vector<int> edge[MAXN];

ull shift(ull x) {
    x ^= mask;
    x ^= x << 13;
    x ^= x >> 7;
    x ^= x << 17;
    x ^= mask;
    return x;
}
void dfs(int x, int p) {
    h[x] = 1;
    vector<ull> son;
    for (int i : edge[x]) {
        if (i == p) continue;
        dfs(i, x);
        son.push_back(h[i]);
        h[x] += shift(h[i]);
    }

    if(!symmetrical.count(h[x])) {
        map<ull, int> cnt;
        for(ull e : son) cnt[e]++;//不同子树出现的次数
        int odd = 0, bad = 0;
        for(auto e : cnt) {
            if(e.second & 1) {
                odd++;
                bad += !symmetrical[e.first];
            }
        }
        symmetrical[h[x]] = odd < 2 && bad == 0;//出现奇数次的子树不超过1种且是对称的
    }
}
void solve() {
    scanf("%d", &n);
    symmetrical.clear();
    for(int i = 1; i <= n; i++) edge[i].clear();

    for(int i = 1; i < n; i++) {
        int u, v;
        scanf("%d%d", &u, &v);
        edge[u].push_back(v);
        edge[v].push_back(u);
    }
    dfs(1, 0);
    puts(symmetrical[h[1]] ? "YES" : "NO");
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

记录一下题解的做法，不需要哈希函数的写法，直接给每一种子树设置了一个唯一的标识。

```cpp
#include <bits/stdc++.h>

#define int long long
#define pb emplace_back
#define mp make_pair
#define x first
#define y second
#define all(a) a.begin(), a.end()
#define rall(a) a.rbegin(), a.rend()

typedef long double ld;
typedef long long ll;

using namespace std;

mt19937 rnd(time(nullptr));

const int inf = 2e18;
const ll M = 1e9;
const ld pi = atan2(0, -1);
const ld eps = 1e-6;

int last;
map<vector<int>, int> eq;
map<int, bool> symmetrical;

int dfs(int v, int p, vector<vector<int>> &sl){
    vector<int> children;
    for(int u: sl[v]){
        if(u == p) continue;
        children.emplace_back(dfs(u, v, sl));
    }
    sort(all(children));
    if(!eq.count(children)){
        map<int, int> cnt;
        for(int e: children){
            cnt[e]++;
        }
        int odd = 0, bad = 0;
        for(auto e: cnt){
            if(e.y & 1){
                odd++;
                bad += !symmetrical[e.x];
            }
        }
        eq[children] = last;
        symmetrical[last] = odd < 2 && bad == 0;
        last++;
    }
    return eq[children];
}

void solve(int tc){
    int n;
    cin >> n;
    eq.clear();
    symmetrical.clear();
    eq[vector<int>(0)] = 0;
    symmetrical[0] = true;
    last = 1;
    vector<vector<int>> sl(n);
    for(int i = 1; i < n; ++i){
        int u, v;
        cin >> u >> v;
        sl[--u].emplace_back(--v);
        sl[v].emplace_back(u);
    }
    cout << (symmetrical[dfs(0, 0, sl)]? "YES" : "NO");
}

bool multi = true;

signed main() {
    int t = 1;
    if (multi)cin >> t;
    for (int i = 1; i <= t; ++i) {
        solve(i);
        cout << "\n";
    }
    return 0;
}
```

