## 介绍

**这篇文章是博客 [SOS Dynamic Programming [Tutorial]](https://codeforces.com/blog/entry/45223) 的译文**，感谢作者[usaxena95](https://codeforces.com/profile/usaxena95)。

在这篇文章中，我将分享关于如何使用动态规划解决涉及计算子集求和 (**Sum over Subsets**) 问题的小知识。这就是为什么命名为**SOS DP**。我选择这个主题是因为它在竞赛中经常作为中等难度及以上的问题出现，但很少有博客/社论解释其背后有趣的 DP。

## 问题

我将解决以下问题：给定一个包含 *$2^N$* 个整数的固定数组 **A** ，我们需要计算 ∀ x 函数 **F(x)** = 所有 **A[i]** 的总和，要求 **x&i = i**，即 **i** 是 **x** 的子集。
$$
F[mask] = \sum_{i \subseteq mask} A[i]
$$

## 前置知识

- 基本动态规划
- 位掩码

## 解决方案

#### 暴力破解

```c
for(int mask = 0;mask < (1<<N); ++mask){
	for(int i = 0;i < (1<<N); ++i){
		if((mask&i) == i){
			F[mask] += A[i];
		}
	}
}
```

该解决方案非常简单且效率低下，时间复杂度为 *O($4^N$)*

#### 次优解

```c
//遍历所有掩码
for (int mask = 0; mask < (1<<n); mask++){
	F[mask] = A[0];
    //遍历掩码的所有子集
    for(int i = mask; i > 0; i = (i-1) & mask){
    	F[mask] += A[i];
    }
}
```

这个解决方案的时间复杂度为 *O($3^N$)* 。对于每个掩码，我们仅对其子集进行迭代。 因此，如果掩码有 *K* 个位，我们将进行 *$2^K$* 次迭代。位上有 *K* 的掩码总数是 $\binom{N}{K}$ 。 因此总迭代次数 = $\sum_{k=0}^{N} {\binom{N}{K} 2^k} = (1+2)^N = 3^N$。

#### SOS动态规划解决方案

在这种方法中，我们将尝试以更智能的方式迭代掩码的所有子集。我们之前的方法中一个明显缺陷是索引 $A[x]$  当 $x$ 有 $k$个 0 时被 $2^k$个掩码访问了。因而有重复的计算。
造成这种开销的一个原因是我们没有在不同 $F[mask]$ 使用的 $A[x]$ 之间建立任何关系。我们必须以某种方式向这些掩码添加另一个状态并制作语义组以避免重新计算。

定义 $S(mask) = \{x | x \subseteq mask \}$。现在我们将把这个集合分成不相交的组。
$S(mask,i) = \{ x | x \subseteq mask \&\& mask \bigoplus x \lt 2^{i+1} \}$，这个集合由掩码的子集构成，这些子集仅在前 i 位（从零开始）与掩码不同。
例如 $S(1011010,3) = \{1011010,1010010,1011000,101000\}$。使用它，我们可以将任何集合表示为一些非相交集合的并集。

让我们尝试关联这些数字集。 $S(mask, i)$ 包含掩码的所有子集，它们仅在前 $i$ 位不同。
考虑掩码的第 $i$ 位为 0。在这种情况下，任何子集都不能与掩码的第 $i$ 位不同，否则 $mask \bigoplus x \ge 2^{i+1}$ 不满足定义条件。因此，该集合中的数字现在只能在前 $i-1$ 位不同。即有 $S(mask,i) = S(mask,i-1)$。
考虑掩码的第 $i$ 位为 1。现在属于 $S(mask, i)$ 的数字可以分成两个不相交的集合。 一个包含第 $i$ 位为 1 且在接下来的 $i-1$ 位中与掩码不同的数字。另一个包含第 $i$ 位为 0 的数字，并且在接下来的 $i-1$ 位中与 $mask \bigoplus 2^i$ 不同。即有 $S(mask,i) = S(mask,i-1) + S(mask \bigoplus 2^i,i-1)$。
$$
S(mask,i) =
\begin{cases}
S(mask,i-1), \quad i^{th}=0 \\
S(mask,i-1) \cup S(mask \bigoplus 2^i,i-1), \quad i^{th}=1
\end{cases}
$$
下图描述了我们如何将 $S(mask,i)$ 相互关联起来。任何集合 $S(mask,i)$ 的元素都是其子树中的叶节点。红色前缀表示掩码的这部分将对其所有成员/子项都是通用的，而掩码的黑色部分允许不同。

![SOS_DP_tree](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20221226104350.png)

请注意，这些关系形成了有向无环图，不一定是有根树（考虑不同的掩码值和相同的 $i$ 值）
了解了这些关系后我们就可以很容易的得出相应的动态规划。

```c
//迭代版本
for(int mask = 0; mask < (1<<N); ++mask){
	dp[mask][-1] = A[mask];	//单独处理叶子结点
	for(int i = 0;i < N; ++i){
		if(mask & (1<<i))
			dp[mask][i] = dp[mask][i-1] + dp[mask^(1<<i)][i-1];
		else
			dp[mask][i] = dp[mask][i-1];
	}
	F[mask] = dp[mask][N-1];
}
```

```c
//内存优化，超级容易编码
for(int i = 0; i<(1<<N); ++i)
	F[i] = A[i];
for(int i = 0;i < N; ++i) for(int mask = 0; mask < (1<<N); ++mask){
	if(mask & (1<<i))
		F[mask] += F[mask^(1<<i)];
}
```

上述算法时间复杂度为 *O($N2^N$)* 。

## 问题讨论

现在您知道了如何计算固定数组 $A$ 的子集求和。如果 $A$ 和 $F$ 互为 SOS 函数会怎样。考虑对问题进行以下修改。假设 H1、H2 是 32 位整数值散列函数（只是为了避免任何组合方法来规避这个问题）并且可以在恒定时间内的任何时间点进行计算。
$$
F[mask] = H1(\sum_{i \subseteq mask} G[i]) \\
G[mask] = H2(\sum_{i \subseteq mask} F[i])
$$

## 练习题

我希望你喜欢它。 以下是建立在 SOS 上的一些问题。

- [Special Pairs](https://www.hackerearth.com/problem/algorithm/special-pairs-7/?utm_source=header&utm_medium=search&utm_campaign=he-search)
- [Compatible Numbers](https://codeforces.com/contest/165/problem/E)
- [Vowels](https://codeforces.com/contest/383/problem/E)
- [Covering Sets](https://www.codechef.com/problems/COVERING)
- [COCI 2011/2012 Problem KOSARE](http://hsin.hr/coci/archive/2011_2012/contest6_tasks.pdf)
- [Vim War](https://www.hackerrank.com/contests/w16/challenges/vim-war)
- [Jzzhu and Numbers](https://codeforces.com/problemset/problem/449/D)
- [Subset](https://www.hackerrank.com/contests/countercode/challenges/subset)
- [Jersey Number](https://icpcarchive.ecs.baylor.edu/index.php?option=com_onlinejudge&Itemid=8&category=635&page=show_problem&problem=4997)
- [Beautiful Sandwich](https://www.codechef.com/SNFL16MR/problems/BEAUTY)
- [Pepsi Cola](https://codeforces.com/group/qcIqFPYhVr/contest/203881/problem/K)(resembles above *discussion problem*). Need to join [this](https://codeforces.com/group/qcIqFPYhVr/join) group.
- [Uchiha and Two Products](https://www.hackerearth.com/problem/algorithm/uchiha-brothers-and-two-products-circuit/)(resembles above *discussion problem*)
- [Strange Functions](https://www.codechef.com/IPC15P2B/problems/STR_FUNC)(Same as above *discussion problem*)
- [Varying Kibibits](https://codeforces.com/contest/800/problem/D)

**EDIT**: 练习题现在几乎按难度递增的顺序排列。

## 额外

看到了一条极其有趣的评论

我从 errichto 的视频中学到了另一种很酷的可视化/记忆 SOS 的方法：

如何将一维数组转换为其前缀和？ 你这样做：`a[i] += a[i - 1]`。
如何将二维数组转换为其前缀和？ 通常这样做： `p[i][j] = p[i-1][j] + p[i][j-1] - p[i-1][j-1] + a[i][j]`。但请注意，您还可以分别在行和列中应用一维前缀和：
`for i in [1, N]: for j in [1, N]: a[i][j] += a[i][j-1]  `
`for i in [1, N]: for j in [1, N]: a[i][j] += a[i-1][j]`

现在，子集求和问题可以想象为在 2×2×…×2 的超立方体上做前缀和！例如，假设掩码有 3 位，并且您希望对 101 的子掩码求和。这相当于在 3D 立方体上从单元格 (0,0,0) 到 (1,0,1) 求和。

因此，您可以分别在每个维度上应用一维前缀和。这正是最终版本的代码做的事情。它首先在维度上进行迭代，然后对该维度执行 `a[..][1][..] += a[..][0][..]`；换句话说，在该维度上采用前缀和。在那之后，初始数组变成了 SOS！

[视频准确时间的链接](https://youtu.be/Lpvsd8WpbWc?t=311)

