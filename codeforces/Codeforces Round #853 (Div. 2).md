**比赛链接**：<https://codeforces.com/contest/1789>

## A. Serval and Mocha's Array

**题意**：对于一个正整数数组 $a$，如果 $a$ 中所有元素的最大公约数不大于它的长度，则该数组被定义成好的。现在给定一个包含 $n$ 个正整数的数组 $a$ ，请确定数组 $a$ 是否可以通过对数组 $a$ 中的元素重新排序来使得所有长度不小于2的前缀数组都是好的。允许数组 $a$ 保持不变。

**解法**：`gcd(a[1],a[2])`总是所有前缀数组最大公约数中的最大值，如果第一个前缀数组是好的，则所有的前缀数组都是好的。判断是否存在两个元素满足最大公约数不超过2即可。

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
int a[MAXN];
void solve() {
    scanf("%d", &n);
    for(int i = 1; i <= n; i++) scanf("%d", &a[i]);

    for(int i = 1; i <= n; i++) {
        for(int j = i + 1; j <= n; j++) {
            if(__gcd(a[i], a[j]) <= 2) {
                puts("Yes");
                return;
            }
        }
    }
    puts("No");
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## B. Serval and Inversion Magic

**题意**：给定一个01字符串，问能否翻转一段区间使得字符串是个回文串。

**解法**：判断字符串是否形如`[√√√][xxx][√√√][√√√][xxx][√√√]`这样的模式。

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
char a[MAXN];
void solve() {
    scanf("%d", &n);
    scanf("%s", a + 1);

    int l = 1, r = n, flag = 0;
    while(l < r) {
        int numl = a[l] - '0';
        int numr = a[r] - '0';
        if(numl ^ (flag % 2) != numr) {
            if(flag <= 1) flag++;
            else {
                puts("No");
                return;
            }
        }
        l++;
        r--;
    }
    puts("Yes");
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## C. Serval and Toxel's Arrays

**题意**：给定一个无重复元素的数组，每次操作改变一个元素得到一个新数组，保证每次操作得到的数组中元素都是不重复的。两两组合每个数组，求每种组合不同元素数量的总和。

**解法**：注意元素的取值范围为 $1\le a_{i}\le n+m$ 且每次只修改一个元素，可以记录每种元素出现在不同数组的次数，通过次数计算出对答案的贡献。

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
int a[MAXN], p, v;
LL cnt[MAXN];
void solve() {
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= n + m; i++) cnt[i] = 0;
    for(int i = 1; i <= n; i++) {
        scanf("%d", &a[i]);
        cnt[a[i]] = m + 1;
    }

    for(int i = 1; i <= m; i++) {
        scanf("%d%d", &p, &v);
        cnt[a[p]] -= m - i + 1;
        cnt[v] += m - i + 1;
        a[p] = v;
    }

    LL ans = (LL)m * (m + 1) * n;
    for(int i = 1; i <= n + m; i++) {
        ans -= cnt[i] * (cnt[i] - 1) / 2;
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

## D. Serval and Shift-Shift-Shift

**题意**：有两个 $n(1\le n\le2\cdot10^{3})$ 位的二进制整数 $a$ 和 $b$，现在想执行不超过 $n$ 次操作将 $a$ 变成 $b$。操作有以下两种。

- $a\oplus(a\ll k)$
- $a\oplus(a\gg k)$

其中移位操作溢出的位被删除，缺失的位用 0 填充，$1 \leq k \leq n$。请给出任意的可行方案或者输出无解。

**解法**：异或运算要想修改目标位的值，只能是去异或1。如果两个整数中有一个全为0而另一个有非0的位，则永远无法发生修改。现在考虑要修改的情况，注意使用最高位的1右移去修改低位的值，则要修改的目标位的左边高位始终是异或0，也就是不会被修改。反之使用最低位的1左移去修改高位的值也有类似的效果。那可以使用下面的步骤在不超过 $n$ 次操作的情况下使得两数相等。

1. 将 $b$ 的最低位1的位置记为$lb$，使用最高位或者最低位将 $a$的 $lb$ 位置修改为1。
2. 使用 $a$ 的最高位右移依次修改 $lb-1$ 到 $1$ 的值，最终低于 $lb$ 的位置全为0。
3. 使用 $a$ 的最低位左移依次修改 $lb+1$ 到 $n$ 的值，最终整数 $a$ 变成了 $b$，且每一位最多被操作一次。

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
string sa, sb;
void solve() {
    cin >> n >> sa >> sb;
    bitset<2000> a(sa), b(sb), hz;
    for(int i = 0; i < n; i++) hz.set(i, 1);

    if((a.none() && b.any()) || (a.any() && b.none())) {
        puts("-1");
        return;
    }

    vector<int> ans;
    int lb = b._Find_first();
    if(a[lb] != b[lb]) {//先处理最低位
        int la = a._Find_first();
        if(la < lb) a ^= a << (lb - la);
        else a ^= a >> (la - lb);
        a &= hz;
        ans.push_back(lb - la);
    }

    for(int i = lb - 1; i >= 0; i--) {//右边的用最高位处理
        if(a[i] == b[i]) continue;
        for(int j = n - 1; j >= 0; j--) {
            if(!a.test(j)) continue;
            a ^= a >> (j - i);
            a &= hz;
            ans.push_back(i - j);
            break;
        }
    }
    for(int i = lb + 1; i < n; i++) {//左边的用最低位处理
        if(a[i] == b[i]) continue;
        int la = a._Find_first();
        a ^= a << (i - la);
        a &= hz;
        ans.push_back(i - la);
    }

    int len =  ans.size();
    printf("%d\n", len);
    for(int i = 0; i < len; i++) printf("%d%c", ans[i], i == len - 1 ? 10 : 32);
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## E. Serval and Music Game

**题意**：给定 $n(1\leq n\leq 10^6)$ 个正整数满足 $1\leq s_1 < s_2 < \ldots < s_n \leq 10^7$，定义函数 $f(x)$ 为下列表达式有非负整数解的下标的数量。求解 $\sum_{x=1}^{s_n} x\cdot f(x)$ 对 $998\,244\,353$ 取模的结果。
$$
s_i=p_i\left\lfloor{s_n\over x}\right\rfloor + q_i\left\lceil{s_n\over x}\right\rceil
$$
**解法**：定义 $k = \left\lfloor{s_n\over x}\right\rfloor$，遍历每一个可能的 $x$ 值

- 如果 $x$ 不是 $s_n$ 的因子，表达式变为 $s_i = p_i \cdot k + q_i \cdot (k+1) = (p_i+q_i)\cdot k + q_i$。要求有非负整数解，则 $s_i$ 的取值范围为 $[0, 0], [k, k+1],\dots, [i \cdot k, i \cdot k+i],\dots, [k \cdot k, +\infty]$。
- 如果 $x$ 是 $s_n$ 的因子，表达式变为 $s_i = (p_i + q_i) \cdot k$，则 $s_i$ 的取值范围为 $k$ 的倍数。
- 可以预处理一个前缀数组维护每个区间中 $s_i$ 出现的次数，就可以在 $O(s_n\log s_n)$ 的情况下得到每个 $f(x)$。

```cpp
#include <bits/stdc++.h>
using namespace std;
#define LL long long
const int INF = 0x3f3f3f3f;     ///1 061 109 567
const int negative_infinite = 0xcfcfcfcf;   ///-808 464 433
const double pi = acos(-1);
const LL mod = 998244353;
const double eps = 1e-8;
const int MAXN = 1e7 + 117;
const int MAXM = 1e6 + 117;

int T = 1;
int n;
int s[MAXN];
int cnt[MAXN];
void solve() {
    scanf("%d", &n);
    for(int i = 1; i <= n; i++) scanf("%d", &s[i]);

    for(int i = 0; i <= s[n]; i++) cnt[i] = 0;
    for(int i = 1; i <= n; i++) cnt[s[i]]++;
    for(int i = 1; i <= s[n]; i++) cnt[i] += cnt[i - 1];

    LL ans = 0, preFx = 0;
    for(int x = 1; x <= s[n]; x++) {
        LL fx = 0 , k = s[n] / x;
        if(s[n] % x != 0) {
            if(s[n] % (x - 1) != 0 && s[n] / (x - 1) == k) fx = preFx;
            else {
                for(int i = 1; i < k && i * k <= s[n]; i++) {
                    int l = i * k;
                    int r = min(l + i, s[n]);
                    fx += cnt[r] - cnt[l - 1];
                }
                if(k * k <= s[n]) fx += cnt[s[n]] - cnt[k * k - 1];
            }
        } else {
            for(int i = k; i <= s[n]; i += k)
                fx += cnt[i] - cnt[i - 1];
        }
        ans = (ans + x * fx) % mod;
        preFx = fx;
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

## F. Serval and Brain Power

**题意**：定义字符串 $T$ 是强大的，当且仅当 $T$ 可以通过多次连接某个字符串 $T'$ 来获得，要求 $k \geq 2$。
$$
T=\underbrace{T'+T'+\dots+T'}_{k\text{ times}}
$$
给定一个由小写英文字母组成的字符串 $S(|S|\leq 80)$。 求解 $S$ 的最长强大子序列的长度。 如果 $S$ 的所有非空子序列都不强大，则认为答案为 0。

**解法**：$|S|\leq 80$ 是解题的关键点。

- 考虑 $k=2$ 的情况，令 $S=S_1+S_2$，$T' = \operatorname{LCS}(S_1, S_2)$，该操作的时间复杂度不会超过 $O(|S|^3)$。
- 考虑 $k=3$ 的情况，令 $S = S_1 + S_2 + S_3$，$T' = \operatorname{LCS}(S_1, S_2, S_3)$，该操作的时间复杂度为 $O(w_3\cdot|S|^5)$。其中 $w_3\leq {1\over 54}$。
- 考虑 $k=4$ 的情况，被 $k=$2 的情况所包含。
- 考虑 $k \geq 5$ 的情况，如果将 $S$ 分成5部分，则 $T'$ 一定是其中一部分的子序列，这让我们可以考虑枚举 $T'$ 。将 $S$ 均分成5部分，每一部分最长不会超过16，其中的子序列最多有 $2^{16}$ 个，再计算每个子序列在 $S$ 中出现的次数即可，该操作的时间复杂度为 $O(5\cdot 2^{|S|/5}|S|)$。

看题解这题并不难理解，但是真的很难想到啊，另外编码还是有点难度的，代码参考了[Appleblue17](https://codeforces.com/contest/1789/submission/196585985)大佬的提交。使用`namespace `来隔离变量名，使用位运算来枚举子序列，写的实在是太优雅了。

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
char s[88];
int ans;

namespace case2 {
int dp[88][88];
void solve() {
    for(int t = 1; t < n; t++) {
        int len1 = t, len2 = n - t;
        for(int i = 1; i <= len1; i++) {
            for(int j = 1; j <= len2; j++) {
                dp[i][j] = max({dp[i - 1][j], dp[i][j - 1]});
                if(s[i] == s[j + t]) dp[i][j] = dp[i - 1][j - 1] + 1;
            }
        }
        ans = max(ans, dp[len1][len2] * 2);
    }
}
}

namespace case3 {
int dp[88][88][88];
void solve() {
    for(int t = 1; t < n - 1; t++) {
        for(int t2 = t + 1; t2 < n; t2++) {
            int len1 = t, len2 = t2 - t, len3 = n - t2;
            for(int i = 1; i <= len1; i++) {
                for(int j = 1; j <= len2; j++) {
                    for(int k = 1; k <= len3; k++) {
                        dp[i][j][k] = max({
                            dp[i - 1][j][k],
                            dp[i][j - 1][k],
                            dp[i][j][k - 1]
                        });
                        if(s[i] == s[j + t] && s[i] == s[k + t2])
                            dp[i][j][k] = dp[i - 1][j - 1][k - 1] + 1;
                    }//k
                }//j
            }//i
            ans = max(ans, dp[len1][len2][len3] * 3);
        }//t2
    }//t
}
}

namespace case5 {
int lent;
char t[88];
int cal() {//计算t作为子序列在s中出现了几次
    int ti = 1, cnt = 0;
    for(int i = 1; i <= n; i++) {
        if(s[i] == t[ti]) ti++;
        if(ti > lent) ti = 1, cnt++;
    }
    return (cnt > 1) * lent * cnt;
}
void solve() {
    for(int l = 1; l <= n; l += 16) {//枚举区间
        int r = min(n, l + 15);
        int len = r - l + 1;
        for(int mask = 1; mask < (1 << len); mask++) {//枚举子序列
            lent = 0;
            for(int i = 0; i < len; i++) {
                if(mask >> i & 1) t[++lent] = s[l + i];
            }
            ans = max(ans, cal());
        }
    }
}
}

void solve() {
    scanf("%s", s + 1);
    n = strlen(s + 1);
    case2::solve();
    case3::solve();
    case5::solve();
    printf("%d\n", ans);
}
int main() {
    //scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```
