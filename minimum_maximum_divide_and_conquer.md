```metadata
title: 最值分治
date: 2026-03-29 02:30
category: 数据结构
difficulty: medium
```

> 特别感谢刘岱同学提供了这篇文章的思路和代码。

## 从一道简单的题目引入

### 题目描述

小明今天突发奇想，想从一张用过的纸中剪出一个长方形。

为了简化问题，小明做出如下规定：

（1）这张纸的长宽分别为 $n,m$。小明将这张纸看成是由 $n\times m$ 个格子组成，在剪的时候，只能沿着格子的边缘剪。

（2）这张纸有些地方小明以前在上面画过，剪出来的长方形不能含有以前画过的地方。

（3）剪出来的长方形的大小没有限制。

小明看着这张纸，想了好多种剪的方法，可是到底有几种呢？小明数不过来，你能帮帮他吗？

### 输入格式

第一行两个正整数 $n,m$，表示这张纸的长度和宽度。

接下来有 $n$ 行，每行 $m$ 个字符，每个字符为 `*` 或者 `.`。

字符 `*` 表示以前在这个格子上画过，字符 `.` 表示以前在这个格子上没画过。

### 输出格式

仅一个整数，表示方案数。

### 输入输出样例 #1

**输入 #1**

```
6 4
....
.***
.*..
.***
...*
.***
```

**输出 #1**

```
38
```

### 说明/提示

【数据规模】

对 $10\%$ 的数据，满足 $1\leq n\leq 10,1\leq m\leq 10$

对 $30\%$ 的数据，满足 $1\leq n\leq 50,1\leq m\leq 50$

对 $100\%$ 的数据，满足 $1\leq n\leq 1000,1\leq m\leq 1000$

### 思路分析

我们可以枚举剪出来的长方形的左上角和右下角的位置，暴力枚举的复杂度为 $O(n^2m^2)$，显然不可行。

优化一小步，我们可以预处理出每个位置向下连续的 `.` 的数量，此时我们只需要枚举左上角的位置和右下角的列数，复杂度为 $O(nm^2)$，仍然不可行。

我们发现，剪出来的长方形的大小是由它的左上角和右下角的位置决定的，而这两个位置又受到剪出来的长方形中是否有 `*` 的限制。

> 因此，我们可以考虑将这个问题这样思考：
>（1）枚举剪出来的长方形的左上角的位置，求出所有满足条件的右下角的位置。
>（2）快速在所有满足条件的右下角的位置中求出最大的右下角位置。
> 这道题目就可以这样使用**斜率优化**解决了，复杂度为 $O(nm)$。

当然这个思考方式其实不是最简单的做法，最简单的做法我们考虑枚举的不再是一个角落，而是一个具有更好性质的点：

> 我们发现，我们可以只考虑，无法向上下左右扩展的剪出来的长方形（**极大**长方形）
> 极大长方形的大小必定受到某一列的限制：所有列的高度都不超过某个值，这个值由最短的列决定。
> 因此我们可以枚举这个最短的列的位置，求出所有满足条件的极大长方形的尺寸。
> 对于每个位置，我们可以使用**单调栈**快速求出满足条件的极大长方形的尺寸，复杂度为 $O(nm)$。 （我写的时候没关自动补全，前面AI都说的不对，这句话AI能自动补全出来）

### 思路之后 what is under monotonous stack

对于一个序列问题，我们优化的问题有如下形式：

$$\operatorname{X}_{1 \le i \le j \le n} f(i, j)$$

$$f(i,j) = \operatorname{Y}_{i \le k \le j} g(k) * h(i,j)$$

X 可以是 $\min$ 或 $\max$ 或 $\sum$， Y 可以是 $\min$ 或 $\max$。

这类问题我们可以考虑枚举每一个 $k$，使用单调栈，求出满足条件的 $i$ 和 $j$ 的范围，复杂度为 $O(n)$。

这里还要求如果 $X$ 是 $\sum$，则 $h(i,j)$ 需要能快速计算出 $\sum_{x=i}^j \sum_{y=x+1}^j h(x,y)$。

如果 $X$ 是 $\min$ 或 $\max$，则 $h(i,j)$ 需要满足单调性。

此时，如果我们把每个 $k$ 的 $i$ 和 $j$ 的范围画出来，我们会发现这些范围的包含关系是树形的（即**笛卡尔树**），因此我们可以使用分治的方式求解。

> 极值分治的本质其实是建笛卡尔树，在笛卡尔树上分治。
> ::::info[笛卡尔树]
> 
> [OI-wiki链接](https://oi-wiki.org/ds/cartesian-tree/)
> 
> 笛卡尔树是一种二叉搜索树，每个节点有一个权值，节点权值两两不同，在满足二叉搜索树的条件下还要满足堆的条件，即父节点权值要比子节点小（有时是大）。
> ::::

传统线性分治一般是将一个区间$[l, r]$拆分成两个子区间（子问题）$ [l, \text{mid}]$和$[\text{mid} + 1, r]$最后再合并解决，这样可以达到$T(n) = T(\frac{n}2) + \mathcal{O}(n) = \mathcal{O}(n \log n)$的优秀复杂度。

但是有些题目里的式子包含最大值/最小值，使得我们不能快速计算出当前问题的最值（你用DS暴力做当我没说），下面是几道例题：

## [P4755 Beautiful Pair](https://www.luogu.com.cn/problem/P4755)

> 题意简述
> 
> 给定一个数列$\{ a \}$，求$|\{(i, j) : i \le j \land a_ia_j\le\max_{k=i}^ja_k\}|$.
> 
> 其中$1 \le n \le 10^5, 1 \le a_i \le 10^9$.

朴素暴力做法复杂度为$\mathcal{O}(n^3)$，显然不可行。

使用ST表或线段树/树状数组可以达到$\mathcal{O}(n^2\log n)$或$\mathcal{O}(n^2)$，也不行。

我们发现，式子里那个$\max$很不好做，有没有办法可以把它消掉呢？这就请出我们的主角：最值分治。

分治时不再选取第$\left\lfloor\frac{l+r}2\right\rfloor$个元素切割，而是选取区间最大值切割。
令区间最大值的下标为$\text{mx}$，则将$[l, r]$切割为$[l, \text{mx - 1}]$和$[\text{mx + 1}, r]$两段再分治。

合并时枚举一边的元素$i$，则该元素的贡献是另一边满足$a_j \le \frac{a_\text{mx}}{a_i}$的$j$的个数，使用可持久化线段树或二次离线（将贡献离线到最后一起计算），单次贡献复杂度$\mathcal{O}(n \log n)$，
总复杂度**期望**为$\mathcal{O}(n \log n)$，合并时记得计算$\text{mx}$的贡献。

考虑一种极端情况：$ \{a\}$为$1,2,3,4,5,6,7,8,9,\cdots,n$，此时我们的做法就被卡到了$\mathcal{O}(n^2\log n)$。
解决方法是**启发式合并**：每次选择短的一个子问题枚举，复杂度优化为$\mathcal{O}(n\log n)$。

> 为什么？
> 
> 当枚举一个长为$n$的子问题时，原问题长度不会低于$2n$，于是每个数被访问的次数为$\mathcal{O}(\log n)$的.
> 
> 总复杂度$\mathcal{O}(n\log n)$.

代码放在最后。

## [P9607 [CERC2019] Be Geeks!](https://www.luogu.com.cn/problem/P9607)

> 题意简述
>
> 给定一个数列$\{ a \}$，求$\sum_{i\le j}(\max_{k=i}^ja_k)(\gcd_{k=i}^ja_k) \bmod 1 \ 000 \ 000 \ 007$.
>
> 其中$1 \le n \le 2 \times 10^5, 1 \le a_i \le 10^9$.

~~写到这我鼠标没电了~~

首先套路地写一个$\mathcal{O}(1)$查询区间$\max$和$\gcd$的ST表，按最大值分治消掉$\max$，朴素合并复杂度为$\mathcal{O}(n^2)$，不可接受。

注意到$\gcd$有以下性质：

>$\gcd(a_1, a_2, \cdots, a_n, a_{n+1}) \le \gcd(a_1, a_2, \cdots, a_n)$且如果$\gcd(a_1, a_2, \cdots, a_n, a_{n+1}) \lt \gcd(a_1, a_2, \cdots, a_n)$则有$\gcd(a_1, a_2, \cdots, a_n, a_{n+1}) \le \frac12\gcd(a_1, a_2, \cdots, a_n)$.
> 
>> 证明：
>> 每次$\gcd$变化至少要除以$2$.

所以对于确定的$l$，$ |\{\gcd_{i=l}^ra_i\}|$是$\mathcal{O}(\log V)$级别的，$ r$同理。

用二分分别找出$\text{mx}$左右两边所有$\gcd$相等的段，再暴力枚举，单次贡献复杂度为$\mathcal{O}(\log^2 V)$，总体复杂度约为$\mathcal{O}(n \log^2 V)$，可以通过。

代码就不放了。

## 习题

[P4755 Beautiful Pair](https://www.luogu.com.cn/problem/P4755)

[P9607 [CERC2019] Be Geeks!](https://www.luogu.com.cn/problem/P9607)

[AT_abc282_h [ABC282Ex] Min + Sum](https://www.luogu.com.cn/problem/AT_abc282_h)

[P11661 无聊](https://www.luogu.com.cn/problem/P11661)

## 代码

```c++
// P4755 Beautiful Pair
#include <bits/stdc++.h>
using namespace std;

using ll = long long;
constexpr ll N = 2e5 + 5, logN = 40, V = 1e9 + 1e7;

int n;
ll a[N];

struct psegtree {
    struct node {
        int ls, rs, cnt;
    } st[N * logN];
    int cnt, root[N];
    void insert(int &rt, const int p, const int ll, const int rr, const int k) {
        if (!rt) rt = ++cnt;
        st[rt] = {0, 0, st[p].cnt + 1};
        if (ll == rr) return;
        const int mid = (ll + rr) >> 1;
        if (k <= mid) st[rt].rs = st[p].rs, insert(st[rt].ls, st[p].ls, ll, mid, k);
        else st[rt].ls = st[p].ls, insert(st[rt].rs, st[p].rs, mid + 1, rr, k);
    }
    int _query(const int rt, const int k, const int ll, const int rr) const {
        if (!rt || ll > k) return 0;
        if (rr <= k) return st[rt].cnt;
        const int mid = (ll + rr) >> 1;
        if (k <= mid) return _query(st[rt].ls, k, ll, mid);
        return st[st[rt].ls].cnt + _query(st[rt].rs, k, mid + 1, rr);
    }
    int query(const int l, const int r, const int k) const { return l <= r ? _query(root[r], k, 1, V) - _query(root[l - 1], k, 1, V) : 0; }
} pst;
// 二次离线这里换成记录查询

struct SparseTable {
    int f[logN][N];
    static int mmax(const int x, const int y) { return a[x] > a[y] ? x : y; }
    void build() {
        for (int i = 1; i <= n; ++i) f[0][i] = i;
        for (int i = 1; 1 << i <= n; ++i)
            for (int j = 1; j <= n - (1 << i) + 1; ++j)
                f[i][j] = mmax(f[i - 1][j], f[i - 1][j + (1 << i >> 1)]);
    }
    int query(const int l, const int r) const {
        const int k = __lg(r - l + 1);
        return mmax(f[k][l], f[k][r - (1 << k) + 1]);
    }
} st;

ll solve(const int l, const int r) {
    if (l > r) return 0;
    if (l == r) return a[l] == 1;
    const int mid = st.query(l, r);
    ll res = pst.query(l, r, 1);
    if (mid - l < r - mid) for (int i = l; i < mid; ++i) res += pst.query(mid + 1, r, a[mid] / a[i]);
    else for (int i = mid + 1; i <= r; ++i) res += pst.query(l, mid - 1, a[mid] / a[i]);
    return res + solve(l, mid - 1) + solve(mid + 1, r);
}

signed main() {
    cin >> n;
    for (int i = 1; i <= n; ++i) cin >> a[i], pst.insert(pst.root[i], pst.root[i - 1], 1, V, a[i]);
    st.build();
    cout << solve(1, n) << endl;
    return 0;
}
```