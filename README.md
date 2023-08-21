# SIGNATE_StdCup_2023

ディレクトリ ツリー
```
.
├── README.md
├── data
│   ├── submit_sample.csv
│   ├── test.csv
│   └── train.csv
├── img
│   ├── train_colomn_infomaiton.png
│   └── train_colomn_infomaiton2.png
└── nb
    └── Untitled.ipynb
```
## Paper
|No.|Status|Name|Detail|Date|Url|
|---|---|---|---|---|---|
|01|Done|Kaggle日記という戦い方|コンペの基本戦術！最初に読む|202009|[URL](https://zenn.dev/fkubota/articles/3d8afb0e919b555ef068)|

## Basics
**Overview**
[URL](https://signate.jp/competitions/1051/data)

### 配布データ
- 学習用データ（train.csv）
- 評価用データ（test.csv）
- 応募用サンプルファイル（submit_sample.csv）
<br>
<img width="972" alt="train_colomn_infomaiton" src="https://github.com/Taito3441/SIGNATE_StdCup_2023/assets/127362083/77c81ce9-f091-4b9a-9057-f9dedf259932">
<br>
<img width="972" alt="train_colomn_infomaiton2" src="https://github.com/Taito3441/SIGNATE_StdCup_2023/assets/127362083/ee91de44-b67d-4513-887b-3f61c354b04b">

## Log
### 20230811
- join!!
- githubの使い方も色々あることだけはわかった！
- データを大まかに理解したい →nb01
   - メソッド知らないしやり方わからんからGPTに聞く →GPT01
<br>
<img width="1011" alt="nb01_" src="https://github.com/Taito3441/SIGNATE_StdCup_2023/assets/127362083/88628dcd-86c7-4ef1-b720-79063b4b6a37">
<br>
<img width="460" alt="GPT01" src="https://github.com/Taito3441/SIGNATE_StdCup_2023/assets/127362083/e5ababb3-d831-41a3-a544-4bbed2cc77a6">


### 20230815
- 全体の投げれを掴むために、一旦SIGNATEのチュートリアルで予測結果を出す。nb01 初提出
  - 評価 95.4502143　←死ぬほど低い笑笑
  - 原因は主に決定木を使っていること&前処理がテキトー
### 20230816
- Slack内で人気のlightgbmを使って予測をしてみる。→nb02
  - 評価は77.35959858323427 少し良くなった笑
  - 前処理のやり方がまだわからん💦
### 20230817
- Slackの情報交換のとこにあったコード何書いてるかわからないけどやってみる。 0726 FermiDさんの投稿
  - nb03  評価  54.167
- 次はこれの改良作があるからそれもまんま映す。 0727 wawawawaさん(当時2位) 参考nb/baseline(1).ipynb
  - nb04  評価  44.20
  - 評価高いのでコードの意味を一文ずつコメントアウトで解説つけていく
### 20230818
- nb04 2回目の投稿 暫定評価43.7830119 リーダーボード順位110位！
- スクールの先生が簡単に参考コード作ってくれたからこれもノートブックに移してコメントアウト入れる。参考nb/古謝さんコンペ参考コード.ipynb
  - nb05　3回目の投稿 暫定評価  65.1200495　　←ローカル環境だともっと高かったんだが…難しい
- nb06を作成　　nb04の予測価格と実際の価格が特に離れているとこの特徴を洗い出す。 
### 20230819
- nb07　nb04の予測値と実測値を比べて特に外れているところを洗い出し対策する。
  - 実測値にはprice4000以上のものもあるが、予測値には全くない。　←値段の高いものの特徴を特徴量として作る。
    - **priceの高いものtop500・top100と全体のデータの３つを比べて、下記のような違いがある。nb通常trainにて比較。全体をa、top100をt1、top500をt5とする。**
      - aのManufactureはfordがダントツだが、t1.t5ではfordとramがツートップ
      - aのCylindersは6,4,8の順に多いが、t1,t5では8が５割、6が４割を占める。
      - aのfuelはgasが９割だがt1ではdieselが7割、t５だとdieselが5割
      - aのdriveはfwdが5割、4wdが３割だが、t1だと4wdが8割、t5だと4wdが7割
      - aのsizeは5割がfull-sizeだが、t1,t5のfull-sizeの割合は9割
      - aのtypeのtruckは1.5割、pickupは1割に満たないが、t1,t5だとtruck5割,pickup2.5割
### 20230820
- nb07 重要だと思った特徴に重りづけになる数字をつけそれらの和を特徴として扱う。評価 44.19 　4回目の投稿 暫定評価43.6998708 順位70位！！
  - その後、この特徴を数値型にしたが、効果は薄くなった。(44.20)
  - 重りづけの数字を変えて、要素となる特徴を追加すると44.17にローカル環境では上昇。だが、5回目の投稿すると暫定評価43.7169173と落ちた。
- nb08 モデルの特徴量重要度を見るとyearとodometerがとても重視されている。→まずはyearに関係する特徴量を作ることが実測priceに近づくのでは
  - まずはyearを三年ごとに区切りカテゴリを作っていく。そこにtypeを文字列として連結させ新たな特徴量を作る。　→ 評価44.15　うまくいった！
  - 次に、高値の車に多い特徴をうまくyearと連結させれば高い価格帯の車も予測できるのでは。"三年区切り＋type＋何か"の組み合わせを探す。
  - "三年区切り＋type＋drive"これがうまくいった。評価44.03　6回目の投稿した。暫定評価43.7738926←ローカルだとnb04の44.20より0.17も良くなっているのになぜ投稿は上がらないんだ!?←投稿の方は最終評価で実測値がまだ半分残っていて最終評価でグンと上がると希望的観測笑
- nb09 新しくmanufacturerl_typeを作成
  - 'manufacturerl_type'も追加した。manufacturer＋typeの文字列特徴量。評価は44.00←嬉しい。今日の投稿限度が残り一回なので投稿はもう少し精度高めてから。
- nb10 odometer_group＋何かの特徴t量を作る。次はodometerの負の値を正に変換し外れ値半分にする。
  - 外れ値を半分より平均値に補完した方が良さげだったから変更。平均値に
  - odometerとyearがモデルの重要度にかなり関係していたので、これら二つを使った特徴量を作成する
  - "odometer_＋三年区切り＋type"の特徴量を作成。ローカルでは驚異の0.3を叩き出した。←妙に計算遅かったし、何が起こったか分からず、評価良すぎて本気で一位になれるかと思った笑
  - 7回目の投稿 暫定評価106.7126488　くそ悪い評価笑　ローカルで予測値と実測値をグラフで見て、最初は俺最強！と思ったがおそらく過学習。**odometerは連続値なのにそのまま扱ったことが原因** だと思われる。
  - "odometer＋三年区切り＋type"の特徴量の一件から、評価がローカルで**44.0を超える**ものは過学習に陥りやすい。←特徴量を作る時は評価があまり高くなりすぎないように&モデルの重要度の高いもの同士を掛け合わせないようにする。
 
### 20230821
- nb11 次は、前回ひどい評価を受けたodometer_＋三年区切り＋typeが、なぜひどい数値だったのか確かめたい。
  - おそらく、3つの特徴量を掛け合わせたからダメだったのでは！？
  - typeだけを消して、odometer_group_year_periodを作成。評価はメモし忘れた💦
  - 8回目の投稿　暫定評価 59.6252113 ←あまり複雑すぎる特徴量は良くないのかもorモデル重要特徴量どうしを掛け合わせると特徴量に偏りが出るのかも？
  - 次は "manufacturer_cylinders"を追加。odometerとyearは重要度が高い上にそれをくっつけて予測したため偏っているのかも。他の特徴量をあらためて足せばプラマイ良くなる？
  - 9回の投稿 間違えて前回と同じファイル投稿しちった笑
  - 10回目の投稿 ローカルで43.78　投稿の暫定評価 43.8281422←ローカルの評価と近くなった！でも4回目投稿の43.699には劣る、、この差なに？
<br>
これまでの投稿の暫定評価から、trainの検証でいくら良い点を取っても、**testの投稿評価とはあくまで別物。**
"odometer＋三年区切り＋type"と"odometer_group_year_period"の特徴量の一件から、評価がローカルで**44.0を超える**ものは過学習に陥りやすい。←特徴量を作る時は評価があまり高くなりすぎないように&モデルの重要度の高いもの同士を掛け合わせないようにする。
もしくは、以前２位だった人のコードをそのまま使わせてもらっていて内容もあまりわかってないのでそのせいかも。評価の仕組みも交差検証法もまだ名前しか知らん。

### 20230822

### 20230823

### 20230824
