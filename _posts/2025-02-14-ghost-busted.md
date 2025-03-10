---
layout: post
title: "幽霊の正体見たり"
date: 2025-02-14 19:48
category: programming FetutEngine
author: ark231
tags: []
---
ようやく謎が解けた、と思われる。  
<!-- more -->
前回のポスト後、エンジンを変更し、回転などのメンバ変数を露出させるスタイルから
変数自体はプライベートにして、アクセサ経由でアクセスするスタイルに変更して、
変数にアクセスしているコードを追跡しやすくしたが、初期化時を除いて問題のカメラ移動部分でしか
アクセスしていないことがわかった。  
そして、変数の値を完全に書き換えるアクセサだけでなく、差分を反映させるアクセサも追加した。
変数をいじるのはその関数内の１行だけという状況になって、ようやく原因が浮かび上がってきた。  
ライブラリでは、回転をクォータニオンの形で保持している。アクセサでも、差分をクォータニオンの形で受け取るが、
クォータニオン同士の演算を実装しておらず、回転行列との相互変換機能と行列同士の演算のみを実装しており、
一旦変更対象の回転と差分を両方回転行列に変換してから掛け、結果をクォータニオンに戻して代入していた。  
問題は、クォータニオンと回転行列の相互変換機能が壊れていて、値によっては回転行列に変換してそのままクォータニオンに戻すだけで
値が化けてしまうという現象が発生していたことだった。  
一応線形代数機能はユニットテストを組んでパスしてはいたのだが、クォータニオンの変換機能に関しては、2通りの値しかテストしておらず、
そのうち１つは浮動小数点数比較の許容誤差($10^{-5}$にしていた)が他のケースと同じだと通らないので、アルゴリズムの精度が悪いのだと考え
許容誤差を定数倍(5倍〜100倍)するというずさんなものだったため、この問題を見逃していた。~~「これだけ誤差がちょっと大きい」じゃないよ、全くもう~~  
他に良い手も思いつかなかったので、各軸を0度から360度まで１度刻みでテストすることにした。流石に4600万通りの行列演算はコンピューターでも時間がかかるが、
数分で計算が終わった。結果は驚異の正答率6.9%。全くもって駄目である。そして、失敗例の中にはnanになっているものもゴロゴロあった。
これで幽霊の謎が解けた。デフォルト状態の回転行列は運よく変換で誤差が(ほとんど？)生じない値で、カメラを回して変換がバグる領域に入ると回転行列との変換時に
少しずつ誤差が生じていき、まるでプログラムが自我を持ったかのような挙動を示したのだ。  
それにしても、最初のテストで見逃したのはある意味奇跡と言えるかもしれない。これだけひどくバグっているコードで、運よく誤差が$10^{-3}$オーダーで収まる
値を選んでしまったわけだから。これがもし $10^0$ オーダーとかだったら流石に許容誤差をいじろうとは考えず、実装を見直したはずである。
(ただよく考えたらクォータニオンの各成分は$[0,1]$に収まるのでそれだけの誤差が出るのは極めて異常事態ではある。)
まあもっと奇跡的に$10^{-5}$に収まってしまって記憶にも止まらない自体にならなかっただけマシである。今回、なんだかんだ許容誤差を誤魔化したおかげで
記憶に止まり、デバッグ時にこの変換機能が原因である可能性に思い至れたわけで、もし最初のテストで完璧に動いていたらより迷宮入りしていた可能性がある。  
解決策として、変換機能を直し、またクォータニオン同士の演算を定義することにする。最終的にはTRSをまとめて一つの行列にして
シェーダーに渡すので変換機能は必要なため、修正する必要がある。また、クォータニオン同士の演算を定義することで
回転を続けた際の累積誤差が減るのではないかと思っている。
