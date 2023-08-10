# 概要
弊社では、サプライチェーンのリスク管理システム「resilire」のサービスを提供しています。
1年近くサービスを提供するなかで、実際の運用を通じて仮説を検証し、データ構造についてある程度言語化できていったこと
また、当初想定していたよりもずっとデータの量をさばく必要があることが判明した…などの理由から、インフラからのリアーキ…簡単に表現すると「つくりなおし」をしています。
開発正社員3人という状況・新しい製品をゼロから作るなかで、現行製品の保守をどうやってやりくりしているかを綴っていきます。
最後に、この記事を読んでいる人に響くかはわかりませんが、「ひとりで製品を保守する」状況での心構えも遺しておきます。

## 状況
①現行製品では実運用に耐えることができていないところが顕在化しており、一刻も早いリアーキが求められている状態
②弊社社員は3名+リアーキ判断時弊社に関わって頂いている相談役兼業務委託が1名
　ドメイン知識に造詣深く、リアーキ判断のきっかけになった業務委託A
　リアーキにモチベーションを持って「ひとりめ社員」として入社、最初の一歩目から関わっている社員B
　社員A、Bの手が足りていないところをまかないつつ、抽象を具体に落とし込む社員C
　…は、リアーキに集中させて進捗加速させたい
③お客様にはリアーキに開発工数を割くことはご理解頂いている状況だが、運用に支障をきたす不具合は修正していかないといけない
　**運用に支障をきたす不具合がいっぱいある**

…えーっと、社員ABCをリアーキに集中させたくて、現行製品の保守をしないといけない…と…
誰がやるんですかね？　もしかして私ひとりでは？

## つまり、ひとりで保守
弊社製品はリスク情報を検知してサプライチェーンの状況を可視化するというもの。
つまり、災害発生時に利用がスパイクします。問い合わせも災害発生直後にスパイクします。
現行製品はそもそも実運用に耐えられていない状況で、不具合も仕様バグもまだまだ潜んでいる…
そんな状況をひとりで守り切ることになってしまいました。

私が保守を始める時点で、開発チームとしては以下のように保守する　ということを決めました
- 回避手段がなく、運用に支障をきたすような"不具合"しか修正しません

### 弊社製品への問い合わせ特徴
弊社製品は、サプライチェーンの連絡網をつくりつつ、リスク（特に災害）発生時に安否確認をする…という特性上、
地震や大雨などの災害発生時に製品へのアクセスがスパイクするという特徴があります。つまり、問い合わせも災害発生後に集中します。
これが辛いところで…　要は、問い合わせが殺到する時期を予測できず制御できないわけです。天に祈るしかないが、ここは災害大国ニッポン…

### どんどん積重なっていくタスク、超えるキャパ
瞬間的に殺到する問い合わせ、そもそもデータ量に耐えられないDB、書いた人が誰もいなくなったスパゲティコード、保守はひとり。
容易に想像できると思いますが、あっという間にタスクは積み重なり、問い合わせに対し応答が鈍いことにお客様の温度感も上がっていきます。
そうすると、もともとは些細なできごとでもタスクの温度感と優先度が上がり、全部優先度最高になり…
そもそも優先度の高いタスクしかテーブルに乗っていなかったのに、そのテーブルが溢れんばかりの状況かつアッチアチに仕上がっていました。

## 救世主登場
そんな状況でヘルプに入ってくれたのが、ドメイン知識に造詣が深く、弊社初期からお客様とも密にコミュニケーションをとっていた業務委託Aでした。

### 混乱時における、画期的なタスクのさばき方
Aがまずやったのは、大量のタスクの色分けでした。優先度が比較的低いものから高いものまでをグルーピングし、
- 「何に」問い合わせが集中しているのか
- 「何が」できなくて・わからなくて顧客はこまっているのか
…を、整理しました。
そしてひとつひとつのタスクとしては優先度が中〜高だが、問い合わせがよく集まっている機能の対応を優先すると判断したんです。しかも不具合修正ではなく機能改善…
これは私としてはかなり意外なことでした。当初の「運用に支障をきたす不具合修正しかしない」という方針にも当てはまらない判断だったからです。

しかし結論から言うと、この方針は大成功でした。結果として「顧客の機嫌がよくなった」のです。
結果、対応しなければ行けない不具合タスクが減った…わけではありませんが、少なくともすべてのタスクの温度感がフルマックスな状況ではなくなりました。

おそらく私は以下を把握できていなかったのだ、と反省しました。
- 不具合があることくらい、お客様はわかっているし工夫して使おうとしてくださる（※弊社の場合）
- 問い合わせに対してアクションがずっと無いと温度感があがる
- 機能に対して「わからない」ことがあると、それが不具合でなくても運用に支障をきたす

わからない→問い合わせる→反応がない→温度感があがる→不信感→不具合→…

この状況を打破するために、「わからない」を潰す対応は、全体の温度感を下げるためにやるべきことでした。

# 本記事での学び：「ひとりで製品を保守する」状況での心構え
## ひとりじゃない！
いきなり「ひとりで保守」を全否定するようなことを言いますが、ひとりではない！ということをちゃんと把握することが一番大事。
そもそも利用されているお客様がいますし、そこに寄り添う弊社CS、開発陣だけでなく仲間はちゃんといるし、調整しなければいけない相手がいることを認識しましょう。
当たり前のようですが、これをちゃんと把握することによって以下のように事態が好転します
- ひとりで黙々とタスク着手しているときの孤独感がなくなる
- 自分のキャパを超えそうなタスクについて、きちんと相談相手や調整相手を見つけられる
開発タスクに着手している人間が自分ひとりのときこそ、製品に関わる人間の相関図を頭に入れておくべきです。

## 心を鬼にして優先度をつけろ！
工数が1なので、できることには限界があります。自分でも・組織でもそれをきちんと認識して、優先度をつけましょう。
困っている当事者は全部優先度が高いような説明をしますし、実際高いのかもしれません。心を鬼にしましょう。
個人的には「回避手段がなく運用が止まってしまうようなこと」「製品の本質的な価値を損なうこと」を優先度のものさしにしていました。
前述したように「顧客の機嫌がよくなる」というカードも、うまいタイミングで使えるようになりたいですね…

## まだまだ対応は続く…
この「ひとり保守体制」はリアーキ終わるまで続きます。
まだまだ「ひとり」はひよっこですが、うまくまわりを巻き

ちなみに保守だけやってるわけじゃなくて、リアーキも手伝ってるよ！
大変なので仲間を募集しています。話を聴いてみたいな！という方、保守ではなくリアーキ側に関われるので安心してご連絡ください。
https://recruit.resilire.jp/for-engineers