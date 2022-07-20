**比赛链接**：<https://codeforces.com/contest/1660>

## A. Vasya and Coins

**题意**：$a$个1分硬币和$b$个2分硬币，问不能组成的最小的数是多少?

**解法**：很显然，如果至少有1个1分硬币的话那么可以把所有的2分硬币都使用掉。

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
LL a, b;
void solve() {
    cin >> a >> b;
    if(a > 0) cout << a + b * 2 + 1 << "\n";
    else cout << 1 << "\n";
}
int main() {
    cin >> T;
    while(T--) {
        solve();
    }
    return 0;
}
```

## B. Vlad and Candies

**题意**：$n$个数字，每次选择最大值中的一个减1，但不能连续两次选择同一个数，问能不能把所有的数减到0。

**解法**：只要最大值和次大值相差不超过2，就可以交替选择当前的最大值把所有的数减到0。

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
    scanf("%d", &n);
    for(int i = 0; i < n; i++) scanf("%d", &a[i]);
    sort(a, a + n);
    if(n == 1) return a[0] == 1;
    if(a[n - 1] > a[n - 2] + 1) return false;
    return true;
}
int main() {
    scanf("%d", &T);
    while(T--) {
        if(solve()) puts("Yes");
        else puts("No");
    }
    return 0;
}
```

## C. Get an Even String

**题意**：给定一个字符串，要求删除若干个字符获得一个偶数长度的子串，且满足子串的偶数位和前面的一个奇数位字符相同，问最少删除多少个字符。

**解法**：定义$dp[i]$表示前$i$个字符中满足条件的最长子串

- 若$s[i]$在前$i-1$个字符中没有出现过，则$dp[i]=dp[i-1]$。
- 若$s[i]$在前$i-1$个字符中最后一次出现是在位置$j$，则$dp[i]=max(dp[i-1],dp[j-1]+2)$。

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
char s[MAXN];
int dp[MAXN];
int pre[77];
void solve() {
    scanf("%s", s + 1);
    n = strlen(s + 1);
    for(int i = 0; i < 77; i++) pre[i] = 0;

    dp[1] = 0;
    pre[s[1] - 'a'] = 1;
    for(int i = 2; i <= n; i++) {
        int last = pre[s[i] - 'a'];
        dp[i] = dp[i - 1];
        if(last) dp[i] = max(dp[i], dp[last - 1] + 2);
        pre[s[i] - 'a'] = i;
    }

    printf("%d\n", n - dp[n]);
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## D. Maximum Product Strikes Back

**题意**：给定一个值在$[-2,2]$之间的整数数组，删除一个前缀和一个后缀之后剩余数相乘，求怎么删除使得乘积最大？

**解法**：

- 删除全部元素乘积为1，我们只需要考虑正乘积的情况即可。
- 只考虑正乘积，则剩余的数中一定不能有0。
- 现在有一个不含0的子数组，如果子数组中负数的数量为奇数，则删除最前面的负数或者最后面的负数。

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
int a[MAXN], cnt[MAXN];
void solve() {
    scanf("%d", &n);
    int l = n, r = 0, ans = 0, ji = 0, ou = 0, symbol = 0;
    for(int i = 1; i <= n; i++) {
        scanf("%d", &a[i]);
        if(a[i] == 0) {
            cnt[i] = 0;
            ji = 0;
            ou = i;
            symbol = 0;
            continue;
        }

        if(a[i] < 0) {
            symbol = (symbol + 1) % 2;
            if(ji == 0) ji = i;
        }
        cnt[i] = cnt[i - 1] + (a[i] % 2 == 0 ? 1 : 0);
        if(!symbol && cnt[i] > ans) {//正号
            l = ou;
            r = n - i;
            ans = cnt[i];
        }
        if(symbol && cnt[i] - cnt[ji] > ans) {//负号
            l = ji;
            r = n - i;
            ans = cnt[i] - cnt[ji];
        }
    }

    printf("%d %d\n", l, r);
}
int main() {
    scanf("%d", &T);
    while(T--) {
        solve();
    }
    return 0;
}
```

## E. Matrix and Shifts

**题意**：给定一个$n$阶的01矩阵，先对矩阵做任意次循环上移、下移、左移、右移操作，然后每次选择一个格子做亦或操作使得主对角线全为1，其他格子全为0。问最少需要多少次亦或操作？

**解法**：首先明确平移操作是不会影响对角线的相对位置的，也就是说通过平移操作是不会创造出新的对角线组合。那么只需要枚举原来对角线的每种组合，计算所需要的亦或操作次数即可。

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
char s[2077][2077];
void solve() {
    scanf("%d", &n);

    int sum = 0;
    for(int i = 0; i < n; i++) {
        scanf("%s", s[i]);
        for(int j = 0; j < n; j++)
            if(s[i][j] == '1') sum++;
    }

    int ans = n * n;
    for(int i = 0; i < n; i++) {
        int one = 0, zero = 0;
        for(int j = 0; j < n; j++) {
            int x = 0 + j, y = (0 + i + j) % n;
            if(s[x][y] == '1') one++;
            else zero++;
        }
        ans = min(ans, n - one + sum - one);
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

## F1. Promising String (easy version)

**题意**：给定一个长度为$n(1 \leq n \leq 3000)$由`+-`组成的字符串，定义`+-`数量相等的字符串是好的，两个连续的`-`可以替换成一个`+`，问有多少个子串是好的？

**解法**：枚举每个子串，如果可以通过操作使得`+-`数量相等则计入答案。

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
char s[3077];
int f[3077];
void solve() {
    scanf("%d%s", &n, s);
    int ans = 0;
    for(int i = 0; i < n; i++) {
        int zheng = 0, fu = 0, cnt = 0;
        for(int j = i; j < n; j++) {
            f[j] = false;
            if(s[j] == '-') {
                fu++;
                if(j > 0 && s[j - 1] == '-' && !f[j - 1]) {
                    cnt++;
                    f[j] = true;
                }
            } else zheng++;

            if(fu >= zheng && (fu - zheng) % 3 == 0 && (fu - zheng) / 3 <= cnt) {
                ans++;
            }
        }
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

## F2. Promising String (hard version)

**题意**：给定一个长度为$n(1 \leq n \leq 2 \times 10^5)$由`+-`组成的字符串，定义`+-`数量相等的字符串是好的，两个连续的`-`可以替换成一个`+`，问有多少个子串是好的？

**解法**：

- 根据狄利克雷原理(抽屉原理、鸽舍原理)可知，当`-`比`+`多2以上时，必有两个`-`相连。那么当`-`-`+`是3的倍数时字符串是好的。
- 定义$dp[i]$表示前$i$个字符的前缀和，对于每个$i$，我们只需要找到有多少个$j$满足$dp[i] \mod {3}=dp[j] \mod {3},j \lt i$即可。

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
char s[MAXN];
int tree[MAXN][3];
int lowbit(int x) {
    return x & -x;
}
void update(int p, int flag) {
    while(p <= n * 2 + 2) {
        tree[p][flag]++;
        p += lowbit(p);
    }
}
int query(int p, int flag) {
    int ans = 0;
    while(p) {
        ans += tree[p][flag];
        p -= lowbit(p);
    }
    return ans;
}
void solve() {
    scanf("%d%s", &n, s);
    LL ans = 0;
    int num = n + 2;
    for(int i = 0; i <= n * 2 + 2; i++) tree[i][0] = tree[i][1] = tree[i][2] = 0;
    update(num, num % 3);
    for(int i = 0; i < n; i++) {
        if(s[i] == '+') num++;
        else num--;
        ans += query(n * 2 + 2, num % 3) - query(num - 1, num % 3);
        update(num, num % 3);
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