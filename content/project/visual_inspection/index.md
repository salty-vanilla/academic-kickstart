+++
# Date this page was created.
date = 2019-12-01

# Project title.
title = "Visual Inspection"

# Project summary to display on homepage.
summary = "工業部品や食品の外観検査をニューラルネットワークによって自動化"

# Optional image to display on homepage (relative to `static/img/` folder).
# image_preview = "projects/compass1.jpg"

# Tags: can be used for filtering projects.
# Example: `tags = ["machine-learning", "deep-learning"]`
# tags = ["iOS", "Android"]

# Optional external URL for project (replaces project detail page).
external_link = ""

# Does the project detail page use math formatting?
math = true
markup = ""

# Optional featured image (relative to `static/img/` folder).
# [header]
# image = "projects/compass1.jpg"
# caption = "My caption :smile:"

+++

## はじめに
工業製品の製造工程において，出荷製品の品質安定化のために検査は必要不可欠である．
とくに外観検査工程では人による目視検査が主であるが，検査員毎の判断基準のばらつきが長年の課題であった．
近年では深層学習技術の進歩により，これまで困難とされてきた目視検査工程でも自動化が検討されている．
従来の深層学習では正常品と異常品を識別するために大量のサンプルが必要であるが，現実には大量の異常品を確保することは困難である．
そのため，正常品のみ，もしくは正常品と少数の異常品から良否識別できる枠組みが求められている．
また，画像検査においては，1枚の異常画像の中に正常な領域と異常な領域が混在していることがある．
このとき，異常な領域が占める割合が大きければ，検出は容易であるが，小さい場合は難しい．
また，異常には様々な種類が存在し，それぞれを検出するのに適した解像度が存在するはずである．
そのため，本稿では Complementary GANに Multi-scale Patch の枠組みを加えたモデルに正常品のみを学習させ，正常分布とその補集合分布をモデリングし異常検知を行う手法を提案する．

## 従来のNNを使った異常検知

### 差分ベース
* Autoencoder
* AnoGAN
* ADGAN

#### メリット
* 差分ベースなので，欠陥箇所のLocalizationが可能
* 学習が容易

#### デメリット
* 外観検査においては差分の出づらい欠陥が存在する
* Blurが発生し，高周波成分が差分として現れてしまう

### 潜在変数ベース
* Flow-based Model
* Adversarial Autoencoder

#### メリット
* 異常度を対数尤度としてダイレクトに算出できる
* 潜在変数による低次元データの可視化が可能

#### デメリット
* 欠陥情報が消失してしまう
* Out of distributionのデータでも尤度が高くなってしまうことがある．

## Complementary GAN
### そもそもGANとは？
* GeneratorとDiscriminatorの2つのNetworkを持つ
* Generatorは，Discriminatorを騙すように本物に近いデータを生成する
* Discriminatorは，入力が本物のデータなのか・Generatorによって生成された偽のデータなのかを識別する
* GeneratorとDiscriminatorが↑の学習をすることで，Generatorは本物に近いデータを生成できるようになる．

![](https://github.com/NVlabs/stylegan/raw/master/stylegan-teaser.png "style-ganによる生成")

### 結局GANは何を学習している？
* Discriminatorは生成分布とデータ分布の”離れ度合い”を測るDivergence Estimator
* Generatorは算出された”離れ度合い”を最小化する
* その結果，生成分布とデータ分布が近づいていき，本物に近い画像が生成できる

### Discriminatorの出力って異常検知に使える？
* Discriminatorは，本物と偽物が見極められるので，本物を正常データとすれば，正常/異常が分別できるのでは！？という考え
* それは難しい
* Discriminatorが識別しているのは，本物のデータであるか偽物のデータであって，偽物データの中に異常分布の要素は全く含まれていない

### GANとComplementary GANの違いは？
* GANはデータ分布と生成分布を測り，近づける
* Complementary GANはデータ分布と生成分布を測り，データ分布の補集合分布と生成分布を近づける
* つまり，Complementary GANは**正常データには存在しないデータを生成**し，Discriminatorは**正常と正常ではないの識別境界**となる