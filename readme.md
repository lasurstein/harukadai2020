
## Description
### 5/25追記
スクリプトに修正を加えました。
DBは使いまわせそうなので、
nowで`get_relation_words.py`, `count.py`の順で実行してください。
ディレクトリだけ自分の環境に合うよう修正してもらえば大丈夫だと思います。
`migoro_test.ipynb`をJupyter Notebookで起動し
`count.py`で出力される`count_hk_all.txt`と`count_hk_70_soa.txt`を
入力ファイルとすれば結果が出ると思います。
手元のPCにコピーする際は以下リンクが参考になると思います。
https://mibel.cs.tsukuba.ac.jp/wiki/index.php?cmd=read&page=%B8%C0%B8%EC%A5%B0%A5%EB%A1%BC%A5%D7%2F%B8%C4%BF%CD%BB%F1%CE%C1%A5%A2%A1%BC%A5%AB%A5%A4%A5%D6%2F%DF%C0%B2%BC%BE%BB%B9%EE%2F%BF%A7%A4%F3%A4%CA%A5%E1%A5%E2&word=scp
何かエラーが出て解決しなさそうだったら教えてください。
### ---

ツイートデータからバースト検知を利用して桜の見頃を検出するタスクをやってもらいます。
私の研究では東京・石川・北海道のデータで実験したので、別の都道府県３つ程度（例：大阪・京都・福岡）で実験してもらいたいです。

仕組みについてはref/配下のスライドと論文を参考にしてください。
簡単に言うと、桜が見頃の日は、総ツイート数に対する桜に関するツイート数の比率が高くなる（バースト状態）と考えられます。
これを利用して、ツイートデータから見頃を検出します。
さらに、過去の桜の見頃に投稿されたツイートから、桜と関連度の高いキーワード（関連キーワード）を探します。
関連キーワードによって桜に関するツイートの絞り込みを行ってから上記の見頃検出を適用することで、推定精度の改善を図っています。
今回は2014年のデータを教師データとして、2015年のデータから見頃を推定すると良いと思います。

具体的にやってもらうことの流れは以下の通りです。
- now側
	- poisson3から使用するデータをコピーする
	- データをMongoDBに格納する
	- 2014年のデータから関連キーワードを取得する
	- 関連キーワードを利用して、2015年のツイートから日毎の総ツイート数と桜の関連ツイート数を求める
- 手元のPC
	- 日毎の総ツイート数と桜の関連ツイート数を使ってバースト検出を行う

## Script
- script/
	- data_copy.sh
	2014年, 2015年のツイートデータを自分のディレクトリにコピーします
	2014年分は桜の見頃の分しか使わないため2~5月しか記述してないです
	- s_lib.py
	他のスクリプトで使うライブラリです
	- insert_to_mongo.py
	JSON形式のツイートデータをMongoDBに格納できます
	入力：JSON形式のツイートデータ（2014, 2015）
	出力：MongoDBのCollection
	- get_relation_words.py
	教師データから桜の関連キーワードを取得します
	入力：2014年の見頃期間のツイートデータ、2014年の実際の見頃期間
	出力：関連キーワードと関連度がカラムのテキストファイル
	- count.py
	日毎の総ツイート数と桜の関連ツイート数を求められます
	入力：2015年のツイートデータ、get_relation_words.pyで出力したファイル
	出力：[月, 日, 総ツイート数]がカラムになったファイル、[月, 日, 桜の関連ツイート数]がカラムになったファイル
- migoro_test.ipynb
kleinbergのバースト検出とその評価を行えます
jupyter notebookを手元のPCに入れて実行してください。
入力：count.pyで出力したファイル
出力：バースト期間とそのプロットした画像、評価値とそのプロット画像

## sample
sample/配下にサンプルファイルを置いておくので、参考や動作確認に使ってください
