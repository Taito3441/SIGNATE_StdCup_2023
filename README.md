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
- nb06を作成　　nb04の予測価格と実際の価格が特に離れている特徴を洗い出す。 
### 20230819
- nb07　nb04の予測値と実測値を比べて特に外れているところを洗い出し対策する。
  - 実測値にはprice4000以上のものもあるが、予測値には全くない。　←値段の高いものの特徴を特徴量として作る。
    - **priceの高いものtop500・top100と全体のデータの３つを比べて、下記のような違いがある。nb通常trainにて比較。全体をa、top100をt1、top500をt5とする。**
      - aのManufactureはfordがダントツだが、t1.t5ではfordとramがツートップ
      - aのCylindersは6,4,8の順に多いが、t1,t5では8が５割、6が４割を占める。
      - aのfuelはgasが９割だがt1ではdieselが7割、t５だとdieselが5割
      - aのdriveはfwdが5割、4wdが３割だが、t1だと4wdが8割、t5だと4wdが7割
      - aのsizeは5割がfull-sizeだが、t1,t2のfull-sizeの割合は9割
      - aのtypeのtruckは1.5割、pickupは1割に満たないが、t1,t2だとtruck5割,pickup2.5割
### 20230820

### 20230821

### 20230822

### 20230823

### 20230824
