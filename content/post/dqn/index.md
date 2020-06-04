+++
# Date this page was created.
date = 2020-05-30
title = "Deep Q Network"
summary = ""
tags = ["tutorial"]
categories = ["Reinforcement Learning"]
math = true
markup = "goldmark"
+++

## 強化学習の目的
### 変数の定義
* $\mathcal{S}$ : 状態の集合
* $\mathcal{A}$ : 行動の集合
* $P_T(s _ {t+1} \| s_t, a_t)$ : 状態$s_t$で行動$a_t$をしたときにに，状態$s_{t+1}$に遷移する確率を出力する関数（状態遷移確率関数）
* $\pi(a_t \| s_t)$ :　状態$s_t$時の行動$a_t$を選択する確率を出力する関数（政策関数）
* $R(s_t, a_t, s_{t+1})$ :　報酬関数
* $\gamma$ : 割引率

### 目的関数
$$
\mathbb{E}\[ \Sigma^{\infty}_{t=0} \gamma^{t} R(s_t, a_t, s _ {t+1}) \], \forall s_0 \in \mathcal{S}, \forall a_0 \in \mathcal{A}
$$

$$
a_t \stackrel{iid}{\sim} \pi(a_t \| s_t) 
$$
$$
s _ {t+1} \stackrel{iid}{\sim} P_T(s _ {t+1} \| s_t,a_t) 
$$
$$
s_t \in \mathcal{S}, a_t \in \mathcal{A} 
$$
$$
\gamma \in \(0, 1\]
$$

### 最適政策関数
最適政策関数$\pi^*$は以下で表される

$$
\pi^*(a\|s) = \argmax_\pi \mathbb{E}\[ \Sigma^{\infty}_{t=0} \gamma^{t} R(s_t, a_t, s _ {t+1}) \]
$$

また、政策$\pi$で得られる報酬の総和を収益（Return）という
$$
C_t = \mathbb{E}_\pi\[ \Sigma^\infty _ {\tau=0} \gamma^\tau R(s _{t+\tau}, a _{t+\tau}, s _ {t+\tau+1}) \]
$$
$ r_t = R(s_t, a_t, s _{t+1}) $ とすると，$C_t$は以下のような再帰構造を持つ

$$
\begin{aligned}
C_t &= r_t + \mathbb{E}_\pi\[ \Sigma^\infty _ {\tau=1} \gamma^\tau R(s _{t+\tau}, a _{t+\tau}, s _ {t+\tau+1}) \] \\\ &=  r_t + \gamma C _ {t+1}
\end{aligned}
$$


### 状態価値関数

$$
\begin{aligned}
V^\pi(s) &= \mathbb{E}_{\pi} \[ r_0 + \gamma C_1 \| s_0 = s \] \\\ &= \displaystyle\sum _{a \in \mathcal{A}} \pi(a \| s) \displaystyle\sum _{s^{\prime} \in S} P_T(s^{\prime} \| s, a)R(s, a, s^{\prime}) + \gamma \displaystyle\sum _{a \in \mathcal{A}} \pi(a \| s) \displaystyle\sum _{s^{\prime} \in S}P_T(s^{\prime} \| s, a)V^\pi(s^{\prime}) \\\ &= \displaystyle\sum _{a \in \mathcal{A}} \pi(a \| s) \displaystyle\sum _{s^{\prime} \in S} P_T(s^{\prime} \| s, a)\(R(s, a, s^{\prime}) + \gamma V^\pi(s^{\prime}) \)
\end{aligned}
$$

### 状態行動価値関数
$$
\begin{aligned}
Q^\pi(s,a) &= \mathbb{E}_{\pi} \[ r_0 + \gamma C_1 \| s_0 = s, a_0=a  \] \\\ &= \displaystyle\sum _{s^{\prime} \in S} P_T(s^{\prime} \| s, a)R(s, a, s^{\prime}) + \gamma \displaystyle\sum _{a^{\prime} \in \mathcal{A}} \displaystyle\sum _{s^{\prime} \in S}P_T(s^{\prime} \| s, a)\pi(a^{\prime} \| s^{\prime})Q^\pi(s^{\prime},a^{\prime}) \\\ &= \displaystyle\sum _{s^{\prime} \in S} P_T(s^{\prime} \| s, a)\(R(s, a, s^{\prime}) + \gamma \displaystyle\sum _{a^{\prime} \in \mathcal{A}} \pi(a^{\prime} \| s^{\prime})Q^\pi(s^{\prime},a^{\prime})\) 
\end{aligned}
$$

### 状態価値関数と状態行動価値関数の関係
$$
V^\pi(s) = \mathbb{E} _{\pi(a \| s)} \[ Q^\pi(s,a)\]
$$

$$
Q^\pi(s,a) = \mathbb{E} _ {P_T(s^{\prime} \| s,a)} \[ V^\pi(s^{\prime}) \]
$$

### 動的計画法の問題点
状態遷移確率関数（モデル）$P_T(s _ {t+1} \| s_t, a_t)$ が既知なら動的計画法で収益最大化問題が解ける．  
しかし，モデルが未知の場合はモデルフリーな手法が必要となる．  
また，モデルが既知でも状態空間と行動空間が大きい場合は，計算量が膨大になってしまう．

## Q-learning
* 試行錯誤，逐次更新を繰り返すことで，行動価値を直接推定・最適化する（モデルフリー）
* 下式1行目の$r _{t+1} + \gamma \max _{a^\prime \in \mathcal{A}}Q(s _{t+1},a^\prime \)$ が試行錯誤で得られた推定価値

* $\alpha$ : 学習率

$$
\begin{aligned}
Q(s_t, a_t) &\leftarrow (1 - \alpha)Q(s_t,a_t) + \alpha \( r _{t+1} + \gamma \max _{a^\prime \in \mathcal{A}}Q(s _{t+1},a^\prime \)) \\\ &= Q(s_t,a_t) + \alpha \( r _{t+1} + \gamma \max _{a^\prime \in \mathcal{A}}Q(s _{t+1},a^\prime)  - Q(s_t, a_t)\)
\end{aligned}
$$

* Off-policy(政策に依存しない)であるため，任意のpolicyに従いながらQ値の更新が可能．

### epsilon greedy policy
* 確率$\epsilon$で$\mathcal{A}$の中からランダムで行動選択
* 確率$1-\epsilon$で行動価値最大の行動を取る
* 学習が進むにつれて，$\epsilon$を0に近づけることで，決定的にしていく

$$
a^* = \argmax_aQ^\pi(s,a)
$$

$$
\pi^\prime(a\|s) = 
    \begin{cases}
      1-\epsilon + \frac{\epsilon}{\| \mathcal{A}\|}  & \text{ if } a = a^*  \\\\ \frac{\epsilon}{\| \mathcal{A}\|} & \text{ otherwise }
    \end{cases}
$$

### softmax policy (Boltzman policy)
* 温度パラメータ$T$によってランダム性を調整する
* 学習が進むにつれて，$T$を0に近づけることで，決定的にしていく
 
$$
\pi^\prime(a\|s) = \frac{\exp(Q^\pi(s,a)/T)}{\sum_{a^\prime \in \mathcal{A}}\exp(Q^\pi(s,a)/T)}
$$
