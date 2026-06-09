```metadata
title: 卷积，不得不学的魔法操作
date: 2026-06-03 12:00
category: 数学
difficulty: medium+
```

## 卷积的意义

**卷积**在`OI`中经常被使用，但是缺少一个**根本**的好的定义。

但计算机高等教学中大部情况下，在《信号与系统》相关课程中首次出现：~~当然现在大部分计算机本科教育已经完全舍弃这种和现代计算机关系不大的课程~~

此处的系统指的不是我们常说的`操作系统(OS)`，而是指对**输入信号**（激励）施加某种**运算或处理**，从而**产生输出信号**（响应）的任何物理设备或**数学模型** （~~其实操作系统也是一种“系统”，你给他输入，他给你输出。~~）

> LTI 系统——线性时不变系统
> 一个离散时间系统，如果同时满足以下两个条件，就称为 LTI 系统:
> 1. 线性性: 对加权和的响应等于各自响应的加权和 
>> 若
$$
x_1[n] \rightarrow y_1[n], \quad x_2[n] \rightarrow y_2[n]
$$
则对任意常数$a, b$有：
$$
\mathcal{T}\{a x_1[n] + b x_2[n]\} = a\,y_1[n] + b\,y_2[n]
$$
> 2. 时不变性: 系统特性不随时间变化
>> 则对任意常数$a, b$有：
$$
\mathcal{T}\{a x_1[n] + b x_2[n]\} = a\,y_1[n] + b\,y_2[n]
$$

下面我们讨论的系统都是**线性时不变系统**：

对于两个离散时间信号$x[x]$和$h[x]$，它们的**卷积和**（简称卷积）定义为：

$$
y[x] = x[x] * h[x] = \sum_{k=-\infty}^{\infty} x[k] \, h[x-k]
$$

其中：
-$x[x]$：输入信号
-$h[x]$：系统的单位脉冲响应(输入为单位脉冲信号$\delta[n]$时，系统产生的**零状态响应**(没有其他输入的情况下的响应))
-$y[x]$：卷积结果，即输出信号

### 例子：音乐厅作为一个 LTI 系统

音乐厅对声音的传播可以近似为**线性时不变（LTI）系统**：
- **线性**：两次掌声同时存在时，听到的回声是各自回声的叠加。
- **时不变**：无论你何时鼓掌，回声的延迟和衰减规律都一样。

#### 1.脉冲响应: 用单位强度拍手听到的声音序列$h[n]$
在舞台上击掌一次（理想化单位脉冲），你录下的声音序列（直达声 + 两次墙壁回声）为：

$$
h[n] = \{ \underline{1},\; 0.6,\; 0.3 \}
$$

-$n=0$：直达声，幅度最大（1）
-$n=1$：第一次回声，延迟1个时间单位，衰减到 0.6
-$n=2$：第二次回声，延迟2个时间单位，衰减到 0.3
- 其余时刻为零


#### 2. 输入信号：你的两次掌声$x[n]$

- **第一下**：$n=0$时刻用力拍，幅度为$2$
- **第二下**：$n=2$时刻较轻拍，幅度为$1$

$$
x[n] = \{ \underline{2},\; 0,\; 1 \}
$$

（$n=1$时不拍手，幅度为 0）

#### 3. 输出信号：音乐厅在如此拍手情况下的声音序列$y[n]$

$$
y[n] = \sum_{k=-\infty}^{\infty} x[k]\,h[n-k]
$$

**最终输出序列**：

$$
y[n] = \{ \underline{2},\; 1.2,\; 1.6,\; 0.6,\; 0.3 \}
$$

## 卷积的计算

我们了解了卷积的定义，现在可以尝试计算卷积，暴力的计算方式显然是$O(n^2)$

如何更快的计算,我们在之后再考虑。

## 与多项式乘法的关联

$$
y[n] = \sum_{k=-\infty}^{\infty} x[k]\,h[n-k]
$$

与多项式乘法的公式：

$$
f(x) = \sum_{i = -\infty}^\infty a_i \cdot x^i
$$

$$
g(x) = \sum_{i = -\infty}^\infty b_i \cdot x^i
$$

$$
f(x) \cdot g(x) = \sum_{i = -\infty}^\infty c_i \cdot x^i
$$

其中
$$
c_n = \sum_{i = -\infty}^\infty a_k \cdot b_{n-k} 
$$

这个求解$c_n$的式子和卷积计算的式子的运算过程完全相同，说明其求解可以相互转化。

## 高效的计算方式

我们需要用到一个**变换**的工具

考虑到计算 
$$
y[n] = \sum_{k=-\infty}^{\infty} x[k]\,h[n-k]
$$

复杂度很高，我们可以考虑将其拆分成若干个**正交**的信号序列的和。

> 两个信号a,b正交当且仅当其点集为0
>$$a \cdot b = \sum_{k=-\infty}^\infty a[k] \cdot b[k]$$

记$\operatorname{T} (x)[i]$为$x$分解出的第 i 个正交信号，有

$$
    \sum_i \operatorname{T}(x)[i][k] = x[k]
$$

由正交性可知 
$$
y[n] = \sum_{k=-\infty}^{\infty} x[k]\,h[n-k] = \sum_{k=-\infty}^{\infty} \sum_i \operatorname*{T}(x)[i][k]\,
\sum_j \operatorname*{T}(h)[j][n-k] 
=   \sum_i \sum_j\sum_{k=-\infty}^{\infty}
\operatorname*{T}(x)[i][k]\,
\operatorname*{T}(h)[i][n-k]
=\sum_i  \sum_{k=-\infty}^{\infty}  \operatorname*{T}(x)[i][k]\,
\operatorname*{T}(h)[i][n-k]
$$

又有

$$
y[n] = 
\sum_i \operatorname*{T}(y)[i][n] 
$$

整理得

$$
\sum_i \operatorname*{T}(y)[i][n] = \sum_i \sum_{k=-\infty}^{\infty}  \operatorname*{T}(x)[i][k]\,
\operatorname*{T}(h)[i][n-k]
$$

显然一种合法的分解方法为：

$$
\operatorname*{T}(y)[i][n] = \sum_{k=-\infty}^{\infty}   \operatorname*{T}(x)[i][k]\,
\operatorname*{T}(h)[i][n-k]
$$

也就是说如果正交分解后的要满足一下条件

1. 这些函数之间两两正交
2. 这些函数方便计算与自身的卷积
3. 这些函数分解和求和有可以快速计算的方式

那么加速卷积计算就是可行的。

哪里有这么好性质的函数呢？

这就需要我们认识伟大的$e^{ix}$函数了

### 傅里叶变换 (Fourier Transform)

#### 了解你的$e^{ix}$

首先我们需要知道$e^{ix} = cos x + i sin x$（欧拉公式）

也就是说$e^{ix}$是将$1$在复平面上逆时针旋转$x Rad$

我们考虑两个函数：$e^{i\omega_1x}$与$e^{i\omega_2x}$的卷积，其中$\frac{\omega_1\,N}{2 \pi},\frac{\omega_2\,N}{2 \pi} \in \mathbb{Z}$

>因为$\sum_{k=-\infty}^{\infty}$这种形式会涉及到无穷，不方便后续推导，我们保证$\frac{\omega_1\,N}{2 \pi},\frac{\omega_2\,N}{2 \pi} \in \mathbb{Z}$的情况下，求和会每$N$个数字循环，所以仅需要考虑$\sum_{k=0}^{N-1}$

$$(e^{i\omega_1x}* e^{i\omega_2x})(x) = \sum_{k = 0}^{N-1}e^{i\omega_1k}* e^{i\omega_2(x-k)} = \sum_{k = 0}^{N-1} e^{i((\omega_1-\omega_2)k + \omega_2x)}$$

当$\omega_1 \ne \omega_2$时 ,$(\omega_1 - \omega_2)N \in \mathbb{Z} \setminus \{0\}$
容易证明此时$$\sum_{k=0}^{N-1}e^{i((\omega_1-\omega_2)k + \omega_2x)} = 0$$即证得正交性

当$\omega_1 = \omega_2 = \omega$时，$\omega_1 - \omega_2 = 0$

$$(e^{i\omega_1x}* e^{i\omega_2x})(x) = \sum_{k = 0}^{N-1}e^{i((\omega_1-\omega_2)k + \omega_2x)}  = N \cdot e^{i\omega x}$$

我们发现不仅卷积的结果已知，而且计算出的结果直接属于这一类信号，方便我们后续计算。

现在考虑长度为$N$的信号序列$x[n]$，其只在$n \in [0,N) \cap \mathbb{N}$有值。我们列出$N$个常用的复指数基序列：

$$
h_k[n] = \frac{1}{N} e^{i\frac{2\pi n k}{N}}, \quad k \in [0,N) \cap \mathbb{N},
$$
写成矩阵形式，即

$$
\mathbf{U} = \frac{1}{N}
\begin{bmatrix}
1 & 1 & 1 & \cdots & 1 \\
1 & e^{i\frac{2\pi}{N}} & e^{i\frac{4\pi}{N}} & \cdots & e^{i\frac{2\pi (N-1)}{N}} \\
1 & e^{i\frac{4\pi}{N}} & e^{i\frac{8\pi}{N}} & \cdots & e^{i\frac{4\pi (N-1)}{N}} \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
1 & e^{i\frac{2\pi (N-1)}{N}} & e^{i\frac{4\pi (N-1)}{N}} & \cdots & e^{i\frac{2\pi (N-1)^2}{N}}
\end{bmatrix},
$$

其中$\mathbf{U}_{n,k} = \frac{1}{N} e^{i\frac{2\pi n k}{N}}$，$n,k = 0,1,\dots,N-1$。

> 为什么不使用$\displaystyle \tilde{h}_k[n] = \frac{1}{\sqrt{N}} e^{i\frac{2\pi n k}{N}}$（标准正交基）？  
> - **符合 DFT 的惯用公式**：别人都是这么写的，你标新立异小心到时候出bug查不出来：$x[n] = \frac{1}{N}\sum_k X[k] e^{i2\pi nk/N}$。此处$h_k[n]$恰好对应逆变换的基函数，使得系数$b_k$直接等于频域值$X[k]$。  
> - **无额外缩放因子**：虽然矩阵不再是酉矩阵，但正逆变换的数值计算更加直观，且与工程习惯一致，避免了$\sqrt{N}$带来的非整数缩放。

然后我们考虑进行分解：

$$
x[n] = \sum_{k=0}^{N-1} b_k \, h_k[n].
$$

将分解式写成矩阵形式：

$$
\mathbf{x} = \mathbf{U} \, \mathbf{b},
$$

其中：

$$
\mathbf{x} = \begin{bmatrix} x[0] \\ x[1] \\ \vdots \\ x[N-1] \end{bmatrix}, \qquad
\mathbf{b} = \begin{bmatrix} b_0 \\ b_1 \\ \vdots \\ b_{N-1} \end{bmatrix}, \qquad
\mathbf{U} = \begin{bmatrix}
h_0[0] & h_1[0] & \cdots & h_{N-1}[0] \\
h_0[1] & h_1[1] & \cdots & h_{N-1}[1] \\
\vdots & \vdots & \ddots & \vdots \\
h_0[N-1] & h_1[N-1] & \cdots & h_{N-1}[N-1]
\end{bmatrix}.
$$

因此矩阵$\mathbf{U}$的元素为：

$$
\mathbf{U}_{n,k} = \frac{1}{N} e^{i\frac{2\pi n k}{N}}.
$$

我们需要求得$\mathbf{b}$，可以通过求解线性方程组得到：

$$
\mathbf{b} = \mathbf{U}^{-1} \mathbf{x}.
$$

由于$\mathbf{U}$的列**不是**标准正交的（其列范数为$1/\sqrt{N}$），$\mathbf{U}$不是酉矩阵。但我们可以直接计算其逆矩阵：注意到

$$
\sum_{m=0}^{N-1} e^{-i\frac{2\pi m k}{N}} \cdot \frac{1}{N} e^{i\frac{2\pi m n}{N}} = \frac{1}{N} \sum_{m=0}^{N-1} e^{i\frac{2\pi m (n-k)}{N}} = \delta_{k,n},
$$

因此逆矩阵的元素为：

$$
\left(\mathbf{U}^{-1}\right)_{k,n} = e^{-i\frac{2\pi n k}{N}}, \quad k,n = 0,1,\dots,N-1.
$$

即

$$
\mathbf{U}^{-1} =
\begin{bmatrix}
e^{-i\frac{2\pi\cdot0\cdot0}{N}} & e^{-i\frac{2\pi\cdot0\cdot1}{N}} & \cdots & e^{-i\frac{2\pi\cdot0\cdot(N-1)}{N}} \\
e^{-i\frac{2\pi\cdot1\cdot0}{N}} & e^{-i\frac{2\pi\cdot1\cdot1}{N}} & \cdots & e^{-i\frac{2\pi\cdot1\cdot(N-1)}{N}} \\
\vdots & \vdots & \ddots & \vdots \\
e^{-i\frac{2\pi\cdot(N-1)\cdot0}{N}} & e^{-i\frac{2\pi\cdot(N-1)\cdot1}{N}} & \cdots & e^{-i\frac{2\pi\cdot(N-1)\cdot(N-1)}{N}}
\end{bmatrix}.
$$

注意，$\mathbf{U}^{-1}$不再是$\mathbf{U}$的共轭转置，而是其共轭转置的$N$倍，即$\mathbf{U}^{-1} = N \, \mathbf{U}^*$。这与离散傅里叶变换中正逆变换相差一个因子$1/N$的惯例完全一致：若定义$\mathbf{b}$为频域系数，则$\mathbf{b} = \mathbf{U}^{-1} \mathbf{x}$正是 DFT 的正变换公式（无缩放），而$\mathbf{x} = \mathbf{U} \mathbf{b}$是逆变换公式（含$1/N$缩放）。

我们只要进行一次矩阵乘法就可以计算出答案了，而直接计算矩阵乘法的复杂度居然是$O(N^2)$，也就是说我们实际没有进行任何有效的优化。但是更好的结构方便我们进行优化，自习观察这个矩阵我们似乎发现可以进行一个分治算法来快速计算这个特殊的矩阵乘法。

#### FFT：分治算法解救世界

大部分介绍FFT算法的博客是从这里开始的，因为这一部分才到了OI熟悉的领域：

快速傅里叶变换（Fast Fourier Transform, FFT）是计算离散傅里叶变换（DFT）及其逆变换的高效算法，时间复杂度为$O(N \log N)$，相比直接计算的$O(N^2)$有巨大提升。这里以 **Cooley–Tukey 基-2 按时间抽取（DIT）FFT** 为例进行介绍。

由上文矩阵可知离散傅里叶变换定义为：

$$
X[k] = \sum_{n=0}^{N-1} x[n] \, \omega_N^{-nk}, \quad k = 0,1,\dots,N-1,
$$

其中$\omega_N = e^{i\frac{2\pi}{N}}$是$N$次单位根。为简洁，以下记$\omega = \omega_N$，并假设$N$是 2 的幂（方便后续分治，实际应用中N可以取到大于需要范围的最小的 2 的幂）。

**引理 1：单位根的性质（周期性与对称性）**

- **周期性**：$\omega^{k+N} = \omega^k$，即指数模$N$周期。
- **对称性**：$\omega^{k+N/2} = -\omega^k$（当$N$为偶数时）。
- **约化性质**：$\omega_{N}^{nk} = \omega_{N/n}^{k}$（更一般地，$\omega_{N}^{m} = \omega_{N/m}^{1}$，若$m \mid N$）。

这些性质使我们可以合并重复计算，将大 DFT 分解为小 DFT。

**引理 2：奇偶分解（分治原理）**

将长度为$N$的序列$x[n]$按索引奇偶分成两个子序列：

- 偶下标：$x_e[m] = x[2m],\quad m=0,\dots,N/2-1$
- 奇下标：$x_o[m] = x[2m+1],\quad m=0,\dots,N/2-1$

则原序列的 DFT$X[k]$可表示为：

$$
\begin{aligned}
X[k] &= \sum_{m=0}^{N/2-1} x[2m] \, \omega^{-2mk} \;+\; \sum_{m=0}^{N/2-1} x[2m+1] \, \omega^{-(2m+1)k} \\
&= \sum_{m=0}^{N/2-1} x_e[m] \, (\omega^2)^{-mk} \;+\; \omega^{-k} \sum_{m=0}^{N/2-1} x_o[m] \, (\omega^2)^{-mk}.
\end{aligned}
$$

注意到$\omega^2 = e^{i\frac{4\pi}{N}} = e^{i\frac{2\pi}{N/2}} = \omega_{N/2}$，因此上式是两个长度为$N/2$的 DFT：

$$
X[k] = X_e[k] + \omega^{-k} X_o[k], \quad k = 0,\dots,N-1,
$$

其中$X_e[k]$和$X_o[k]$分别是$x_e$和$x_o$的$N/2$点 DFT（周期为$N/2$）。由于$X_e[k]$和$X_o[k]$以$N/2$为周期，只需计算$k=0,\dots,N/2-1$，然后利用周期性得到$k=N/2,\dots,N-1$的值：

$$
\begin{cases}
X[k] = X_e[k] + \omega^{-k} X_o[k], & k = 0,\dots,N/2-1, \\
X[k+N/2] = X_e[k] + \omega^{-(k+N/2)} X_o[k] = X_e[k] - \omega^{-k} X_o[k], & k = 0,\dots,N/2-1.
\end{cases}
$$

其中用到了对称性$\omega^{-(k+N/2)} = -\omega^{-k}$。

#### 算法描述（递归版本）

根据引理 2，计算$N$点 DFT 可递归地进行：

1. 如果$N=1$，直接返回$X[0]=x[0]$。
2. 否则：
   - 将输入序列分成偶下标和奇下标两个子序列。
   - 递归计算两个子序列的$N/2$点 DFT，得到$X_e[0..N/2-1]$和$X_o[0..N/2-1]$。
   - 对$k=0$到$N/2-1$：
     - 计算旋转因子$W = \omega^{-k}$。
     - 令$X[k] = X_e[k] + W \cdot X_o[k]$。
     - 令$X[k+N/2] = X_e[k] - W \cdot X_o[k]$。

复杂度递推$T(N) = 2T(N/2) + O(N)$，解得$T(N)=O(N\log N)$。

#### 蝴蝶变换（Butterfly）优化

上述递归算法中，最内层的两个计算：

$$
\begin{aligned}
X[k] &= X_e[k] + W \cdot X_o[k] \\
X[k+N/2] &= X_e[k] - W \cdot X_o[k]
\end{aligned}
$$

称为 **蝶形运算**，其结构图如下（$W = \omega^{-k}$）：
```plain_text
X_e[k] ──┬── (+) ── X[k]
         │
         │ (W)
         │
X_o[k] ──┴── (-) ── X[k+N/2]
```

- 加法与减法共享同一个乘积$W \cdot X_o[k]$，只需一次复数乘法，两次复数加减法。
- 这种“蝴蝶”结构在整个算法中反复出现，是 FFT 高效性的核心。

在 **迭代（原位）实现** 中，可以避免递归，通过 **位逆序重排** 和 **自底向上的蝴蝶循环** 来完成：

1. **位逆序重排**：将输入数组$x[n]$按照$n$的二进制位倒序重新排列，使递归分治末端的自然顺序变为适合自底向上计算的顺序。
2. **自底向上合并**：设定步长$len = 2,4,8,\dots,N$，在每个阶段对每对相距$len/2$的数据进行蝴蝶变换，旋转因子按层计算（$W = \omega^{-k \cdot (N/len)}$等形式）。

该迭代实现避免了函数调用开销，且所有数据在同一个数组上更新，内存访问局部性好。

通过递归或迭代应用蝴蝶变换，FFT 以$O(N \log N)$的时间复杂度完成 DFT 计算，是数字信号处理中最基本的算法之一。

同时由于FFT及其逆运算的矩阵结构上几乎是相同的，在竞赛时可以使用一个函数复用，同时实现两个功能。

参考代码：

```c++
#include <bits/stdc++.h>
using namespace std;

using cd = complex<double>;
const double PI = acos(-1);

void fft(vector<cd>& a, int inv) {
    int n = a.size();
    // 位逆序置换 (bit-reversal)
    for (int i = 1, j = 0; i < n; ++i) {
        int bit = n >> 1;
        for (; j & bit; bit >>= 1)
            j ^= bit;
        j ^= bit;
        if (i < j) swap(a[i], a[j]);
    }

    // 迭代合并
    for (int len = 2; len <= n; len <<= 1) {
        double ang = 2 * PI / len * (inv ? -1 : 1); // inv=1: 正向, inv=-1: 逆向
        cd wlen(cos(ang), sin(ang));
        for (int i = 0; i < n; i += len) {
            cd w(1);
            for (int j = 0; j < len/2; ++j) {
                cd u = a[i+j], v = a[i+j+len/2] * w;
                a[i+j] = u + v;
                a[i+j+len/2] = u - v;
                w *= wlen;
            }
        }
    }

    // 逆向变换需要除以 n
    if (inv == -1) {
        for (cd& x : a) x /= n;
    }
}
```

### 从 FFT 到 NTT：用原根代替单位根

~~我被复数精度单杀了，快请998,244,353救我~~

在 FFT 中，我们依赖于复数单位根$\omega_N = e^{i\frac{2\pi}{N}}$的以下关键性质：

1. **周期性**：$\omega_N^{k+N} = \omega_N^k$
2. **对称性**：$\omega_N^{k+N/2} = -\omega_N^k$（$N$为偶数）
3. **正交性**：$\frac{1}{N}\sum_{k=0}^{N-1} \omega_N^{k(m-n)} = \delta_{m,n}$
4. **可约性**：$\omega_N^{nk} = \omega_{N/n}^k$（当$n \mid N$）

这些性质使得分治（蝴蝶操作）能够成立，并且 DFT 矩阵是酉的（除以$\sqrt{N}$后）。然而，复数运算在计算机中存在**精度误差**和**速度较慢**的问题（尤其是 `sin` / `cos` 及复数乘法）。

数论变换 (NTT) 的基本思想

#### **数论变换（Number Theoretic Transform, NTT）** 是在有限域（或整数环模一个素数）上定义的 DFT 变体。它将复数单位根替换为**模素数下的原根**，利用整数运算（模乘、模加）实现，从而：

- **无精度误差**：所有运算都是精确的整数模运算。
- **速度更快**：整数模运算比浮点复数运算快得多，且便于向量化。
- **适合卷积**：对于整数系数的多项式乘法，NTT 可直接得到精确结果，无需处理浮点舍入。

#### 原根的定义与性质

**阶 (Order)**
设$p$为素数，$g$是模$p$的一个**原根**（primitive root），如果$g$的阶为$p-1$，即：
$$
g^{p-1} \equiv 1 \pmod p, \quad \text{且对于任意 } 1 \le t < p-1,\ g^t \not\equiv 1 \pmod p.
$$
换句话说，$\{g^0, g^1, \dots, g^{p-2}\}$恰好遍历模$p$的非零剩余。

**用原根构造“单位根”**
在 NTT 中，我们选取一个素数$p$，使得$p-1$是$N$的倍数（通常$N$为 2 的幂）。令
$$
\omega_N \equiv g^{\frac{p-1}{N}} \pmod p.
$$
那么$\omega_N$在模$p$下的阶恰好是$N$，因为：
$$
\omega_N^N \equiv g^{p-1} \equiv 1 \pmod p, \quad \text{且 } \omega_N^{N/2} \neq 1 \ (\text{当 } N>2).
$$

于是$\omega_N$具有与复数单位根完全相同的代数性质：

- **周期性**：$\omega_N^{k+N} = \omega_N^k \pmod p$
- **对称性**：$\omega_N^{k+N/2} = -\omega_N^k \pmod p$（注意这里的$-$是模$p$意义下的加法逆元，因为$\omega_N^{N/2} \equiv -1$成立）
- **正交性**：$\frac{1}{N} \sum_{k=0}^{N-1} \omega_N^{k(m-n)} \equiv \delta_{m,n} \pmod p$，其中$1/N$指模$p$下$N$的乘法逆元
- **可约性**：$\omega_N^{nk} = \omega_{N/n}^k \pmod p$（若$n | N$）

**为什么原根能取代单位根？**
FFT 的推导只依赖单位根的**代数性质**（满足$x^N=1$且$x^{N/2} \neq 1$等），而不依赖复数的具体取值。因此，任何具有相同代数结构的域或环都可以进行类似的变换。有限域$\mathbb{F}_p$中的原根恰好提供了这样的结构：

- **存在性**：对于素数$p$，一定存在原根（且通常很容易找到，例如 998244353 的原根为 3）。
- **阶的条件**：只要$p-1$含有因子$N$，就可以通过幂运算得到$N$阶元素，作为$\omega_N$。
- **逆元**：$N$在模$p$下有逆元（因为$p$是素数且$N \not\equiv 0 \pmod p$），可以完成 IFFT 中的除以$N$操作。

因此，NTT 本质上就是在有限域上执行与 FFT 完全相同的算法，只是将加减乘除替换为模$p$运算，将旋转因子从$\exp$变为$g^{(p-1)/N}$。

#### 常用参数（模数与原根）

竞赛中最常用的 NTT 模数是 **998244353**（$119 \cdot 2^{23} + 1$），它具有以下优点：

- 是素数，原根为$3$。
-$p-1 = 2^{23} \cdot 119$，因此可以支持高达$2^{23}$的点数（即$N$最大为$2^{23}$），足够大多数题目使用。
- 模数较小，乘法结果在 64 位整数范围内，可用快速乘法。

其他常用模数：

| 模数$p$             | 原根$g$| 最大$2$的幂次 |
|----------------------|----------|----------------|
| 998244353            | 3        |$2^{23}$      |
| 1004535809           | 3        |$2^{21}$      |
| 469762049            | 3        |$2^{26}$      |
| 167772161            | 3        |$2^{25}$      |

NTT 代码实现

以下代码实现了 NTT 和 INTT（逆 NTT），接口与前面 FFT 保持一致（通过 `inv` 控制方向）：

```cpp
#include <bits/stdc++.h>
using namespace std;

using ll = long long;

const int MOD = 998244353;   // 素数
const int ROOT = 3;          // 原根

ll modpow(ll a, ll e) {
    ll res = 1;
    while (e) {
        if (e & 1) res = res * a % MOD;
        a = a * a % MOD;
        e >>= 1;
    }
    return res;
}

void ntt(vector<ll>& a, int inv) {
    int n = a.size();
    // 位逆序置换
    for (int i = 1, j = 0; i < n; ++i) {
        int bit = n >> 1;
        for (; j & bit; bit >>= 1)
            j ^= bit;
        j ^= bit;
        if (i < j) swap(a[i], a[j]);
    }

    // 迭代合并
    for (int len = 2; len <= n; len <<= 1) {
        ll wlen = modpow(ROOT, (MOD - 1) / len);
        if (inv == -1) wlen = modpow(wlen, MOD - 2); // 逆元
        for (int i = 0; i < n; i += len) {
            ll w = 1;
            for (int j = 0; j < len/2; ++j) {
                ll u = a[i+j];
                ll v = a[i+j+len/2] * w % MOD;
                a[i+j] = (u + v) % MOD;
                a[i+j+len/2] = (u - v + MOD) % MOD;
                w = w * wlen % MOD;
            }
        }
    }

    // 逆变换需要乘以 n 的逆元
    if (inv == -1) {
        ll inv_n = modpow(n, MOD - 2);
        for (ll& x : a) x = x * inv_n % MOD;
    }
}
```