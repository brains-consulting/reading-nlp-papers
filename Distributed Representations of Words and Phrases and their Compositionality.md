# Distributed Representations of Words and Phrases and their Compositionality

## 目的

`Efficient estimation of word representations in vector space`[8]で提案されたskip-gramモデルは
構造化されていない大量の文書データから単語のベクトル表現を学習するのに効率的な方法である。

ここでは、

- 学習の高速化
- 少ない単語でのベクトル表現の精度向上

を図る。

## Skip-gramモデル

skip-gramは以下(1)式を最大化することが目的

<img src="https://latex.codecogs.com/gif.latex?\frac{1}{T}\sum_{t=1}^{T}\sum_{-c\leq&space;j\leq&space;c,&space;j\neq&space;0}&space;\log&space;p(w_{t&plus;1}\mid&space;w_{t})" />

- <img src="https://latex.codecogs.com/gif.latex?w_{1},w_{2},...,w_{T}" />: training words
- `c`: training context size

`c`が大きくなれば精度は上がるが計算時間も伸びる。

<img src="https://latex.codecogs.com/gif.latex?p(w_{t&plus;1}\mid&space;w_{t})" />はソフトマックス関数を使って以下(2)式のように定義される。

<img src="https://latex.codecogs.com/gif.latex?p(w_{O}\mid&space;w_{I})=\frac{\exp(v'{_{w_{O}}}^{\top}v_{w_{I}})}{\sum_{w=1}^{W}\exp(v'{_{w}}^{\top}v_{w_{I}})}" />

- <img src="https://latex.codecogs.com/gif.latex?v_{w}" />: `w`のベクトル表現の入力
- <img src="https://latex.codecogs.com/gif.latex?v'_{w}" />: `w`のベクトル表現の入力
- `W`: ボキャブラリ数

この式の計算時間は`W`に比例して大きい。

### 階層的ソフトマックス

フルソフトマックスの効率的な近似として利用する。

計算すべきノード数が<img src="https://latex.codecogs.com/gif.latex?\log_{2}(W)" />に減る。

### Negative Sampling

階層的ソフトマックスの代替として雑音対照推定(NEC: Noise Contrastive Estimation)がある。

skip-gramモデルにおいてはベクトル表現の精度だけが問題なので、より簡素化したNegative Sampling(NEG)を提案する。

### 頻出単語のサブサンプリング

`in`とか`the`は大量に出て来るが情報が少ない。
skip-gramでの共起性も`the`との連携は多くなる。

単語間の頻出さと希少さの不均衡を(4)式によって均す。

## Empirical Results

[8]で紹介したanalogical reasoning taskを使用。

具体的にはSyntaxとSemanticについて以下の様なことをする。

- Syntax `vec('quick') - vec('quickly') + vec('slow') = vec('slowly') ?`
- Semantic `vec('Germany') - vec('Berlin') + vec('France') = vec('Paris') ? `

NEGが階層的ソフトマックスやNECよりも精度が高い。
サブサンプリングも精度向上に役立っている。

##  Learning Phrases

(6)式に基いてbigramのscoreを計算して閾値(不明)以上をphraseとする。

上記同様、analogical reasoning taskを実施。

サブサンプリングが精度向上に寄与することが明らか。

訓練データを増やすと最大72%まで向上した。
