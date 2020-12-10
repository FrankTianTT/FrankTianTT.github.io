---
title: 用Bellman算子理解动态规划
date: 2020-12-10 11:48:30
tags:
---

这片文章算是对Distributional DQN的补充内容，介绍了一些value-based的基本概念以及收敛性的证明。

下面的内容主要是对这个[slides](https://web.stanford.edu/class/cme241/lecture_slides/BellmanOperators.pdf)的整理。

<!--more-->

## Value Functions as Vectors

首先，我们尝试把一个**函数**看作一个**向量**，这可能是泛函中最基本的概念。

我们假设在状态空间$\mathcal{S}$中有$n$个状态

$\left\{s_{1}, s_{2}, \dots, s_{m}\right\}$

在动作空间$\mathcal{A}$中有$m$个状态

$$\left\{a_{1}, a_{2}, \dots, a_{m}\right\}$$

当然在连续的情形就是$n = \infty$或者$m = \infty$，不过它们依旧可以表示为一系列向量。

我们的随机策略定义为$\pi(a | s)$。

那么值函数定义为$\mathbf{v}: \mathcal{S} \to \mathbb{R}$，当然值函数是针对某个策略$\pi$而言的，也就是

$$\mathbf{v}_{\pi}: \mathcal{S} \to \mathbb{R}$$

最优值函数被定义为

$$\mathbf{v}_{*}(s)=\max _{\pi} \mathbf{v}_{\pi}(s)$$

定义$\mathcal{R}_{s}^{a} $是在状态$s$做动作$a$得到奖赏的期望。

定义$\mathcal{P}_{s, s^{\prime}}^{a}$是在状态$s$做动作$a$到达状态$s^\prime$的概率。

那么我们可以用$\mathbf{R}_{\pi}(s)$表示对于这个策略$\pi$，在状态$s$下可能获得的“奖赏的期望”的期望。（第一个期望来自环境的不确定性，第二个期望来自策略的不确定性）

\mathbf{R}_{\pi}(s)=\sum_{a$\in \mathcal{A}} \pi(a | s) \cdot \mathcal{R}_{s}^{a}$

用$\mathbf{P}_{\pi}\left(s, s^{\prime}\right)$表示对于这个策略$\pi$，从状态$s$转移到状态$s^\prime$的概率。

\mathbf{P}_{\pi}\left(s,$s^{\prime}\right)=\sum_{a \in \mathcal{A}} \pi(a | s) \cdot \mathcal{P}_{s, s^{\prime}}^{a}$

我们用$\mathbf{R}_{\pi}$表示向量$\left[\mathbf{R}_{\pi}\left(s_{1}\right), \mathbf{R}_{\pi}\left(s_{2}\right), \ldots, \mathbf{R}_{\pi}\left(s_{n}\right)\right]$，用$\mathbf{P}_{\pi}$表示矩阵\left[\mathbf{P}_{\pi}\left(s_{i},$s_{i^{\prime}}\right)\right], 1 \leq i, i^{\prime} \leq n$。

定义$\gamma$为MDP折扣因子。

Bellman Operators

补充，算子operator，可以粗浅的理解为

函数的输入是集合，输出是集合

泛函的输入是函数，输出是集合

算子的输入是函数，输出是函数

Bellman$Policy Operator \mathbf{B}_{\pi} $作为一个算子，是针对策略$\pi$而言的，它作用于函数$\mathbf v$之上

\mathbf{B}_{\pi}$\mathbf{v}=\mathbf{R}_{\pi}+\gamma \mathbf{P}_{\pi} \cdot \mathbf{v} 

\mathbf{B}_{\pi} $是一个线性的算子，有不动点$\mathbf v_\pi$，即$\mathbf{B}_{\pi} \mathbf{v}_{\pi}=\mathbf{v}_{\pi}$。

这个不动点由$\mathbf{B}_{\pi} $决定，本质上是由$\mathbf{R }_{\pi} $，$\mathbf{P}_{\pi} $和$\gamma$决定。

还有算子Bellman$Optimality Operator \mathbf{B}_{*}$，被定义为

\left(\mathbf{B}_{*}$\mathbf{v}\right)(s)=\max _{a}\left\{\mathcal{R}_{s}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s, s^{\prime}}^{a} \cdot \mathbf{v}\left(s^{\prime}\right)\right\} 

\mathbf{B}_{*}$并不依赖于某个具体的策略。

\mathbf{B}_{*} 也有不动点$\mathbf{v}_{*}$，满足\mathbf{B}_{*}$\mathbf{v}_{*}=\mathbf{v}_{*} $。

这个不动点由$\mathbf{B}_{*}$决定，本质上是由$\mathcal{R}_{s}^{a} $，$\mathcal{P}_{s, s^{\prime}}^{a}$和$\gamma$决定。

接下来我们定义一个greedy的策略$G(\mathbf{v})(s)$，$G$是一个算子，输入是函数$\mathbf{v}: \mathcal{S} \to \mathbb{R}$，输出是函数${\pi}: \mathcal{S} \to \mathcal{A} 

G(\mathbf{v})(s)=\underset{a}{\arg \max }\left\{\mathcal{R}_{s}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s, s^{\prime}}^{a} \cdot \mathbf{v}\left(s^{\prime}\right)\right\}$

这意味着$G(\mathbf{v})$其实就是一个策略$\pi$。

接下来，值得注意的是，对任何值函数$\mathbf{v}$，满足

\mathbf{B}_{G(\mathbf{v})}$\mathbf{v}=\mathbf{B}_{*} \mathbf{v}$

注意$\mathbf{B}_{*} \mathbf{v}$并不一定满足$\mathbf{B}_{*} \mathbf{v} = \mathbf{v}$，上面的式子其实就是做了$\underset{a}{\arg \max }\$和$\underset{a}{\max }\$的转换。

Contraction$and Monotonicity of$Operators

接下来我们定义$\gamma -\text{contraction operators}$，如果一个算子$\mathbf{B}$满足，对任意两个值函数$\mathbf v_1,\mathbf v_2$，有

\left\|\mathbf{B}$\mathbf{v}_{1}-\mathbf{B}\mathbf{v}_{2}\right\|_{\infty} \leq \gamma\left\|\mathbf{v}_{1}-\mathbf{v}_{2}\right\|_{\infty}$

那么我们说这个算子$\mathbf{B}$在$L^{\infty}$上是$\gamma -\text{contraction operators}$。

可以证明，前面的$\mathbf{B}_{\pi} $和$\mathbf{B}_{*}$都满足上面的性质。

我们可以对离散的情况做一个简单的证明，因为状态空间$\mathcal S$是一个有限集，任何策略的值函数都可以表示为$\mathbf v_{\pi} = \{\mathbf v_{\pi}(s_1),\mathbf v_{\pi}(s_2),\cdots,\mathbf v_{\pi}(s_n)\}$

而策略状态转移矩阵$ \mathbf{P}_{\pi} $则是一个$n\times n$的矩阵。

对于$L^{\infty}$，一个有限向量$\mathbf x$的$L^{\infty}$其实就是$\mathbf x$中最大的元素，也就是说

\left\|\mathbf{x}\right\|_{\infty}$= \max_i \mathbf x_i$

实际上，把$\mathbf x$看作一个集合，$L^{\infty}$就是在衡量这个集合的直径。

接下来，我们根据上面的说明做一个简单的证明。

对于Bellman$Policy Operator \mathbf{B}_{\pi} $，有

\begin{aligned}$    \left\|\mathbf{B}_{\pi} \mathbf{v}_{1}-\mathbf{B}_{\pi} \mathbf{v}_{2}\right\|_{\infty}&=\left\| \mathbf{R}_{\pi}+\gamma \mathbf{P}_{\pi} \cdot \mathbf{v_1} -  \mathbf{R}_{\pi}+\gamma \mathbf{P}_{\pi} \cdot \mathbf{v_2}\right\|_{\infty}  \\     &=\left\| \gamma \mathbf{P}_{\pi} \cdot \mathbf{v_1} - \gamma \mathbf{P}_{\pi} \cdot \mathbf{v_2}\right\|_{\infty}  \\     &=\gamma \left\|  \mathbf{P}_{\pi} \cdot (\mathbf{v_1} - \mathbf{v_2})\right\|_{\infty}  \\     &=\gamma  \max _i\mathbf{P}_{\pi} \cdot (\mathbf{v_1} - \mathbf{v_2})_i\\     & \leq \gamma  \max _i (\mathbf{v_1} - \mathbf{v_2})_i \\     & =\gamma\left\|\mathbf{v}_{1}-\mathbf{v}_{2}\right\|_{\infty}\\  \end{aligned}$

中间那个小于等于号很显然，是因为$\mathbf{P}_{\pi}$是一个状态转移矩阵，每行的值都小于$1$，而合等于$1 $，也就是说乘上$\mathbf{P}_{\pi}$相当于做了一次加权平均，自然不会让变得更大。

对于Bellman$Optimality Operator \mathbf{B}_{*}$，我们先说明$\mathbf{B}_{*} \mathbf{v}$的形式，在$\mathbf{v}$是有限集的情况下，$\mathbf{B}_{*} \mathbf{v}$可以被表示成一个向量

\mathbf{B}_{*}$\mathbf{v} = \left(\max _{a}\left\{\mathcal{R}_{s _1}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_1, s^{\prime}}^{a} \cdot \mathbf{v}\left(s^{\prime}\right)\right\}, \\ \max _{a}\left\{\mathcal{R}_{s_2}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_2, s^{\prime}}^{a} \cdot \mathbf{v}\left(s^{\prime}\right)\right\},\\ \cdots\\ \max _{a}\left\{\mathcal{R}_{s_n}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_n, s^{\prime}}^{a} \cdot \mathbf{v}\left(s^{\prime}\right)\right\}\right)$

那么对于$\mathbf{B}_{*} \mathbf{v_1}  - \mathbf{B}_{*} \mathbf{v_2} $，有

\mathbf{B}_{*}$\mathbf{v_1}  - \mathbf{B}_{*} \mathbf{v_2}  =\\ \left(\max _{a}\left\{\mathcal{R}_{s _1}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_1, s^{\prime}}^{a} \cdot \mathbf{v_1}\left(s^{\prime}\right)\right\} - \max _{a}\left\{\mathcal{R}_{s _1}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_1, s^{\prime}}^{a} \cdot \mathbf{v_2}\left(s^{\prime}\right)\right\}, \\  \max _{a}\left\{\mathcal{R}_{s_2}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_2, s^{\prime}}^{a} \cdot \mathbf{v_1}\left(s^{\prime}\right)\right\}-\max _{a}\left\{\mathcal{R}_{s_2}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_2, s^{\prime}}^{a} \cdot \mathbf{v_2}\left(s^{\prime}\right)\right\},\\ \cdots\\ \max _{a}\left\{\mathcal{R}_{s_n}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_n, s^{\prime}}^{a} \cdot \mathbf{v_1}\left(s^{\prime}\right)\right\}-\max _{a}\left\{\mathcal{R}_{s_n}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_n, s^{\prime}}^{a} \cdot \mathbf{v_2}\left(s^{\prime}\right)\right\}\right)$

注意，实际上$\max_a$可以看作对一个长度为$|\mathcal A |$的向量用$L^{\infty}$，也就是$\max_a$满足三角不等式，于是，对于$\mathbf{B}_{*} \mathbf{v_1}  - \mathbf{B}_{*} \mathbf{v_2} $的每一个元素，有

\begin{aligned}$     &\max _{a}\left\{\mathcal{R}_{s_n}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_n, s^{\prime}}^{a} \cdot \mathbf{v_1}\left(s^{\prime}\right)\right\}-\max _{a}\left\{\mathcal{R}_{s_n}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_n, s^{\prime}}^{a} \cdot \mathbf{v_2}\left(s^{\prime}\right)\right\}  \\     &\leq \max _{a}\left\{\mathcal{R}_{s_n}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_n, s^{\prime}}^{a} \cdot \mathbf{v_1}\left(s^{\prime}\right)-\mathcal{R}_{s_n}^{a}+\gamma \sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_n, s^{\prime}}^{a} \cdot \mathbf{v_2}\left(s^{\prime}\right)\right\}\\     &=\gamma \max _{a}\left\{\sum_{s^{\prime} \in \mathcal{S}} \mathcal{P}_{s_n, s^{\prime}}^{a} \cdot \left( \mathbf{v_1}\left(s^{\prime}\right)-\mathbf{v_2}\left(s^{\prime}\right) \right)\right\}\\   \end{aligned}$

同时，无论怎样选择$a$，$\mathcal{P}_{s, s^{\prime}}^{a}$都是一个概率转移矩阵，和$\mathbf{P}_{\pi}$有类似的特点，相当于取加权，于是同理得到

\left\|\mathbf{B}_{*}$ \mathbf{v}_{1}-\mathbf{B}_{*} \mathbf{v}_{2}\right\|_{\infty} \leq \gamma\left\|\mathbf{v}_{1}-\mathbf{v}_{2}\right\|_{\infty}$

证明了$\mathbf{B}_{\pi} $和$\mathbf{B}_{*}$都是$\gamma -\text{contraction operators}$，由Contraction$Mapping$Theorem，我们可以得到$\mathbf{B}_{\pi} $和$\mathbf{B}_{*}$都含有唯一的不动点。

接下来，我们定义值函数之间的小于，如果$\mathbf{v}_{1}\leq \mathbf{v}_{2}$，意味着对于所有的$s$满足

\mathbf{v}_{1}(s)$\leq \mathbf{v}_{2}(s)$

不难证明

\begin{array}{l}$ \mathbf{v}_{1} \leq \mathbf{v}_{2} \to \mathbf{B}_{\pi} \mathbf{v}_{1} \leq \mathbf{B}_{\pi} \mathbf{v}_{2} \\ \mathbf{v}_{1} \leq \mathbf{v}_{2} \to \mathbf{B}_{*} \mathbf{v}_{1} \leq \mathbf{B}_{*} \mathbf{v}_{2}  \end{array}$

也就是说$\mathbf{B}_{\pi} $和$\mathbf{B}_{*}$都是单调的。

Policy Evaluation

有了上面的推导，我们就可以很直观的看待策略评估的过程了。

对于任何策略$\pi$，我们可以得到$\mathbf{B}_{\pi} $，那么策略评估就是在找到$\mathbf{B}_{\pi} $的不动点。

根据Contraction$Mapping$Theorem，对任何值函数$\mathbf v$都有

\lim$_{N \to \infty} \mathbf{B}_{\pi}^{N} \mathbf{v}=\mathbf{v}_{\pi}$

这保证了我们可以从一个随机的起点出发，不断按照Bellman方程更新值函数$\mathbf v$，从而收敛到$\mathbf{v}_{\pi}$。

Policy Improvement

接下来我们分析策略提升的过程。

在第$k$次迭代中，我们计算得到了当前的策略${\pi_{k}}$，对这个策略进行策略评估，我们得到相应的值函数 \mathbf{V}_{\pi_{k}}。

那么策略提升的过程就是令$\pi_{k+1}$满足

\pi_{k+1}=G\left(\mathbf{v}_{\pi_{k}}\right) 

现在我们想证明存在$\pi_{k+1} \geq \pi_{k}$，从而保证策略提升一定会让策略变得更好。

还记得我们之前说

\mathbf{B}_{G(\mathbf{v})}$\mathbf{v}=\mathbf{B}_{*} \mathbf{v}$

于是有

\mathbf{B}_{*}$\mathbf{v}_{\pi_{\mathbf{k}}}=\mathbf{B}_{G\left(\mathbf{v}_{\pi_{\mathbf{k}}}\right)} \mathbf{v}_{\pi_{\mathbf{k}}}=\mathbf{B}_{\pi_{k+1}} \mathbf{v}_{\pi_{\mathbf{k}}}$

根据定义，显然对所有对值函数$\mathbf v$有$\mathbf{B}_{*} \mathbf{v} \geq \mathbf{B}_{\pi} \mathbf{v}$，于是

\mathbf{B}_{*}$\mathbf{v}_{\pi_{k}} \geq \mathbf{B}_{\pi_{k}} \mathbf{v}_{\pi_{k}}=\mathbf{v}_{\pi_{k}}$

结合上面的式子得到

\mathbf{B}_{\pi_{k+1}}$\mathbf{v}_{\pi_{\mathbf{k}}} \geq \mathbf{v}_{\pi_{\mathbf{k}}}$

这说明对值函数进行$\mathbf{B}_{\pi_{k+1}} $算子的运算是一个单调递增的过程，也就是说

\mathbf{B}_{\pi_{k+1}}^{N}$\mathbf{v}_{\pi_{\mathbf{k}}} \geq \ldots \mathbf{B}_{\pi_{k+1}}^{2} \mathbf{v}_{\pi_{\mathbf{k}}} \geq \mathbf{B}_{\pi_{k+1}} \mathbf{v}_{\pi_{\mathbf{k}}} \geq \mathbf{v}_{\pi_{\mathbf{k}}}$

而又有

\mathbf{v}_{\pi_{\mathrm{k}+1}}=\lim$_{N \to \infty} \mathbf{B}_{*}^{N} \mathbf{v}_{\pi_{\mathrm{k}}}=\lim _{N \to \infty} \mathbf{B}_{\pi_{k+1}}^{N} \mathbf{v}_{\pi_{\mathrm{k}}}$

于是

\mathbf{v}_{\pi_{\mathrm{k}+1}}=\lim$_{N \to \infty} \mathbf{B}_{\pi_{k+1}}^{N} \mathbf{v}_{\pi_{\mathrm{k}}} \geq \mathbf{v}_{\pi_{\mathrm{k}}} 

Policy$Iteration

我们之前完成了策略提升的证明，在第$k + 1$个策略迭代中，我们可以保证单调，也就是$\mathbf{v}_{\pi_{\mathbf{k}+1}} \geq \mathbf{v}_{\pi_{\mathbf{k}}}$。

于是，当$\mathbf{v}_{\pi_{\mathbf{k}+1}}=\mathbf{v}_{\pi_{\mathbf{k}}}$，我们就找到了$\mathbf{v}_{*}$，这是因为$\mathbf{v}_{*} \geq \mathbf{v}_{\pi} $，同时$\mathbf{B}_{*}$又只有一个不动点。

Value$Iteration

Value$Iteration的思想就是干脆绕开$\mathbf B_\pi$，直接让$\mathbf B_*$参与运算。

因为我们知道$\mathbf{B}_{*} $有唯一的不动点$\mathbf{v}_{*}$满足$\mathbf{B}_{*} \mathbf{v}_{*}=\mathbf{v}_{*}$，而$\mathbf{B}_{*} \mathbf{v}$又是一个单调递增的运算，因为对于任意的值函数$\mathbf v$，有

\lim$_{N \to \infty} \mathbf{B}_{*}^{N} \mathbf{v}=\mathbf{v}_{*}$

这保证了值迭代的可行性。

Greedy$Policy from Optimal VF is an Optimal$Policy

最后要证明的是Greedy$Policy from Optimal VF is an Optimal$Policy。

其实很简单了，我们已经得到了最优值函数$\mathbf v_*$，我们贪心的选择策略，也就是

\pi_*$= G(\mathbf v_*)$

那么根据最优值函数的定义，我们只需要反过来证明$\mathbf v_{\pi_*}= \mathbf v_*$即可。

首先我们有

\mathbf{B}_{G\left(\mathbf{v}_{*}\right)}$\mathbf{v}_{*}=\mathbf{B}_{*} \mathbf{v}_{*}$

因$\mathbf v_*$是不动点，因此满足

\mathbf{B}_{G\left(\mathbf{v}_{*}\right)}$\mathbf{v}_{*}=\mathbf{B}_{*} \mathbf{v}_{*} = \mathbf{v}_{*}$

这意味着$\mathbf{v}_{*}$也是$\mathbf{B}_{G\left(\mathbf{v}_{*}\right)}$的不动点。而$\mathbf{B}_{G\left(\mathbf{v}_{*}\right)}$又只有一个不动点$\mathbf{v}_{G\left(\mathbf{v}_{*}\right)}$，因此

\mathbf{v}_{*}=\mathbf{v}_{G\left(\mathbf{v}_{*}\right)} 

即

\mathbf$v_{\pi_*}= \mathbf v_*$