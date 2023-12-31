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

- nb12 test投稿評価とtrainのローカルの評価が違いすぎるため、nb07(4回目の投稿で順位70とったやつ)をもとに作り直す。
  - nb07では"custom_score"にしか手を加えていなかったので "odometer_group"と"year_period"を追加 ←評価44.16 nb07の44.20よりはいい!
  - 一度投稿して、ローカルの環境とtestの投稿の評価とでどのような違いがあるのか知りたい。（今のところ44.0を超えたやつは投稿評価が低くなっている。&どの特徴がつけると投稿評価が低くなるのかがわからない）
  - 11回目の投稿 暫定評価 43.6956235 ←nb07より上がっている！！　でも微々たる差で順位は上がらない泣
<br>
今回ので、44.0を超えなければ、少しずつ伸びることがわかった。
"odometer_group"と"year_period"の追加も間違ってはいなかった。
でも上がった評価点は0.004だけ。70-75位から上がった経験がないので効果的だったかはわからない。特徴量を増やすだけではこのまま頭打ちになるのか、たまたま作った特徴量がtestには関係ないものだったのか,,,
<br>

- nb13 Slackで前処理をしても効果が見られないと誰かが言っていて、僕も欠損値やodometerの外れ値をいじってみたがあまり効果が見られなかった。だがどのような特徴量を作るのがいいのかもわからなくなってきたので、日本車とその他の車とで別の値を返す特徴量を作った。→'origin'
  - "origin_custom_score"と"origin_odometer_group"を作った。originだけだとモデルの重要度が低く、効果が薄かったため。
  - 評価 44.12 ← 高すぎずいい感じ！過学習は確実にないはず、かつnb12の44.16よりは高い！
  - 12回目の投稿 暫定評価 43.6767522  ← 過去最高得点！　順位も上がった！周りに抜かれて75位とかだったけ68位くらいにいった。(でも少しだけしか点も順位も上がらないの泣ける)
- nb14 ここで調子に乗ってもう一度ローカルでのスコアの高い"odometer_group_year_period"を特徴量追加して投稿してみる笑
  - "origin_custom_score"と"origin_odometer_group"が一緒なら"odometer_group_year_period"もいい感じになると思ったからだ
  - 評価 43.94
  - 13回目の投稿 暫定評価 43.7422316  ←やっぱダメなのか笑　一発当たれ！と思ったがそう甘くない泣

  
### 20230822
今のところ、 "custom_score","odometer_group","year_period","origin_custom_score","origin_odometer_group",この5つの特徴量をつけてるやつが一番精度がいい。あとローカルでの精度が頼りにならなくなってきている。44.11-44.19あたりが精度良さそうだが、その中の**大小でtestの優劣はつけられない**。もしかすると44.20を超えたとしても投稿testでモッtもいい点が出る可能性も全然ある。
 - nb14 一応、まだ特徴量を付け加えられないかと気になったので、"manufacturer_type"を追加。そしてodometer_groupとorigin_odometer_groupをつけていてodometerに引っ張られすぎているのではないかと思い"odometer_group"のみを削除してやってみる。
   -  評価0.4408　←44.08でだいぶ良い点なのではなかろうか。投稿でもうまくいけば60位を切りそう。
   -  14回目の投稿　暫定評価43.7065456　←やっぱダメだったー！笑　odometer＿groupとmanufacturer_typeのどちらがどう影響したのかもよくわからない、、
 -  nb14　次は、nb07,nb08を見返してcustom_scoreのstr型とint型両方を入れてみることにした。
   -  評価0.4413 "custom_score","year_period","origin_custom_score","origin_odometer_group","odometer_group","manufacturer_type",➕数字型"custom_score"
   -  15回目の投稿 暫定評価　43.6848610　←まあまあいい！ やっぱローカルで44.11-44.19あたりが無難ぽそう
 - nb14 次は"custom_score"を数字型にして、"manufacturer_type"も消す。"year_period","origin_custom_score","origin_odometer_group","odometer_group",➕数字型"custom_score"
   - これで、"custom_score"が数字型が良いのか文字型が良いのかの扱いがわかる。4回目の投稿と、5回目の投稿の時に試したつもりだったが、記録が間違っていたらしく、数字型の方が投稿testで高い評価だったことに気がついた笑
   - 評価44.13 16回目の投稿　暫定評価　43.6633446 ←順位64位に上昇！
   - "custom_score"は数字型のみの方がいいことがわかった。あとマジでローカルの評価がこれ以上信用ならん泣
 - nb14 次はodometer_groupとorigin_odometer_groupと、odometerに関する特徴量が二つ入っており、odometerに少し引っ張られているのではないかと思い、odometer_group単体の方を消した。
   - "year_period","origin_custom_score","origin_odometer_group",➕数字型"custom_score"
   - 評価44.11　←大きすぎず、いい感じなのでは？
   - 17回目の投稿 暫定評価 43.6867129　←odometer_groupが必要な特徴量と再認識できたのでok
 

### 20230823
 - nb15 次は、また他に特徴量を追加したら上がるのではないかと思い、"cylinders2_size",を入れた。cylindersとsizeをくっつけ文字列にしたもの。
   - 評価44.12　←大きすぎず、いい感じなのでは？
   - 18回目の投稿 暫定評価 43.6857383　←クソが！決して悪くはない!惜しい！　これ以上特徴量を増やすのは逆効果なのか？
 - nb16 次はcylinders2_drive
   - 評価は44.12　←nb14と大して違いがないように感じる。testとtrainの違いを見るグラフを見ても一致率がそこまで高くないように感じる。
 - nb17 次はyear_period_originを追加した。originがモデルの重要な特徴量とくっつけると良い精度を出す傾向が強いと思ったからだ。
   - 評価44.13
   - 19回目の投稿 暫定評価 43.7110323 ←なかなか伸びない。小数点以下の評価は最終評価で簡単に揺れたりするのだろうか？
   - 交差検証法を採用してるけど、一番悪い評価をカバーできるようにした方がいいのだろうか、
 - nb17 次はnb14のに、year_period_origin_conditionを追加
   - ぶっちゃけていうと、23:30超えてるのに投稿回数が3回残ってたから慌てて特徴量作って投稿した。
   - 評価44.14
   - 20回目の投稿 暫定評価　43.7102316
 - nb17　次はnb14のやつにcustom_score_conditionを追加。
   - 評価44.14
   - 21回目の投稿　暫定評価　43.6557402 ←67位に上がった。
 - nb18 次はnb17のやつに、transmission_fuelとcylinders2_manufacturerを追加。モデルの特徴量重要度の低いもの同士をくっつけた。
   - 評価44.15
   - 22回目の投稿　暫定評価　43.6600112
 
custom_scoreという特徴量がかなり良いキーポイントになっていそうなので、あらためて重りの数字を考え直してみる

### 20230824
**コンペ最終日**
<br>
testデータとtrainの関係やcvと暫定評価やらの関係を、やっとコンペの全容がつかめてきた気がする。
暫定評価が良くても、最終評価で順位が落ちることもあり得る。暫定評価だけでなく、最終評価を意識した**汎用性のある予測**も重要。←そのために、交差検証法の全てのfoldで良い点をとる。testとtrainの予測値の違いをできるだけなくす。
<br>
今日は、**あと残り５回しか投稿できないので、暫定評価で上に行くのは諦める。その代わり、投稿せずとも評価がある程度確認できる、予測の汎用性を高めることに残り時間を使う。**
1. testのvisualize_oof_pred(oof, pred)のグラフのx軸を12局面に分けて、それぞれの局面でどんな特徴の車が集まっているのかEDA分析をする。←今更笑
1. 改めて、今使っているコードがどのような内容なのか把握する（恥ずかしながら面倒くさがって調べずに使ってました汗）
1. optunaを使う。←Slack情報では、cvは上がったが投稿暫定評価ではあまり効果が見られなかったらしい。
1. visualize_oof_pred(oof, pred)のグラフの一致率と、foldの点を上げて、汎用性の向上に努める



