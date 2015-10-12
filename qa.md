# 「エンジニアのためのベイズ推定入門」質問回答

## 1. pystanのStanコードに日本語コメント

講習中に，probprog3.ipynbにおいて，以下のようにpystanのStanコードに日本語コメント入れておりました．

````
coin_code = """
//並びが重要

//

// カスタマイズ関数の定義
function{}

// stanに渡すデータを宣言する
data {
    int<lower=0> n; // number of tosses
    int<lower=0> y; // number of heads
}

// stanに渡したデータの加工を指示
transformed data {}

// 推定するパラメータを宣言
parameters {
    real<lower=0, upper=1> p;
}

// パラメータを加工したい場合はここで指示
transformed parameters {}

// [必須] 事前確率，尤度をモデリング
model {
    p ~ beta(2, 2);
    y ~ binomial(n, p);
}

// 事後分布からのサンプリングを加工
generated quantities {}
"""

````

しかし，このコードのままでは，asciiエラーが出てしまい，実行できません．

これは，pystanの仕様で，stanにコードを渡すとき（C++によるビルドをするため）に強制的にasciiに文字コード変換してしまうためです．日本語のコメントがあるとこのときにasciiエラーになります．従いまして，pystanでは，stanコードに日本語を使うことができません．大変失礼いたしました．

こちらは，GitHubのリポジトリのほうは修正しておきました．

https://github.com/bakfoo/pyconjp/blob/master/probprog3.ipynb


## 2. pymc3のfind_MAP( )の振る舞い

講習中にpymc3のfind_MAP( )関数の振る舞いについて質問がありました．以下が実際の実装になりますが，

https://github.com/pymc-devs/pymc3/blob/master/pymc3/tuning/starting.py

**find_MAP( )**自体は単に**scipy.optimize.fmin**関数群へのラッパーでした．デフォルトでは**scipy.optimize.fmin_bfgs**が呼ばれていて，**scipy.optimize.fmin_bfgs**は与えられた関数を直接準ニュートン法で解き，最小値を求めます．

http://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.fmin_bfgs.html

事後分布が数値データで与えられた後にMAP推定を求めることは，数値的にはニュートン法で最小値を求めることに他なりませんので正しい振る舞いです．

従いまして，講習中の会話で出てきた，事後分布からサンプリングしているということはありません．