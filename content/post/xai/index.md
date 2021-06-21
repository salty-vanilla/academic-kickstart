+++
date = 2021-05-09
title = "Explanable AI"
summary = "XAIのまとめ"
tags = ["tutorial"]
categories = ["XAI"]
math = true
markup = "goldmark"
draft = true
+++

## CAM系
### 変数の定義
* $F$: CNN
* $x \in \mathbb{R}^{H \times W \times C}$: 入力データ
* $y \in \mathbb{R}^d$: 出力ベクトル $y = F(x)$
* $f$: CNNの最終のFeature Mapを算出する演算 $f(x) \in \mathbb{R} ^{H^\prime \times W^ \prime \times C^\prime}$
* $A$: アテンションマップ

### CAM: Class Activation Map
* CNNの出力層の直前がGAP: Global Average Pooling層であることが条件
* 出力層の全結合層の重みを$w$とする
    * $w^c_i$: 出力層の$c$番目に接続されている$i$番目の重み
    * $ y_c = softmax(GAP(f(x)) \cdot w) $
* 出力層の$c$クラス目のアテンションマップ$A \in \mathbb{R}^{H^\prime \times W^ \prime}$は
$$
A^c = \Sigma_i^{C^\prime}{w^c_i f(x)_i }
$$
{{< figure src="img/cam.png" >}}

### Grad-CAM
* CAMの弱点であるGAP必須によるCNNの識別性能低下を解決
* 出力$y$を$f(x)$で微分した微分MAPがAttention MAPとして機能する
$$
A^c = GAP \left( \frac{\partial y_c}{\partial f\(x\)} \right)
$$

{{< figure src="img/Grad-CAM_approach.png" >}}

#### Guided Grad-CAM

***TBA***
{{< figure src="img/grad-cam_res.png" >}}



