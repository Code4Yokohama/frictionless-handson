# Frictionless Data Hands On

## 準備

### GitPodの起動

下記をブラウザーで開くとGitPodの環境が立ち上がります。
https://gitpod.io/#https://github.com/Code4Yokohama/frictionless-handson

### frictionlessモジュールのインストール

```bash
$ pipenv sync
```

必要なpythonのバージョンが未インストールの場合には、インストールするかどうか聞いてくるので、Yesを選択しましょう。

### 仮想環境に入る

```bash
$ pipenv shell
Launching subshell in virtual environment...
 . /workspace/frictionless-handson/.venv/bin/activate
gitpod /workspace/frictionless-handson (main) $  . /workspace/frictionless-handson/.venv/bin/activate
(frictionless-handson) gitpod /workspace/frictionless-handson (main) $ 
```

プロンプトの先頭に (frictionless-handson) が表示されていれば仮想環境に入っている。抜けたい場合には exit する。

### frictionless CLIが使えることを確認

```bash
(frictionless-handson) gitpod /workspace/frictionless-handson (main) $ frictionless --version
5.8.1
```

## frictionless CLSを試してみる

### 東京都 新型コロナウイルス感染症新規陽性者数のCSVデータを抽出してみる


```bash
$ frictionless extract data/130001_tokyo_covid19_patients_per_report_date.csv # ローカルデータから
$ frictionless extract https://data.stopcovid19.metro.tokyo.lg.jp/130001_tokyo_covid19_patients_per_report_date.csv # URLを直接指定
$ frictionless extract --json data/130001_tokyo_covid19_patients_per_report_date.csv # JSON形式で
$ frictionless extract --yaml data/130001_tokyo_covid19_patients_per_report_date.csv # YAML形式で
$ frictionless extract --help # その他オプションいろいいろ
```

※ https://catalog.data.metro.tokyo.lg.jp/dataset/t000001d0000000011/resource/e2e1c3c7-1c15-44a3-a3f5-142df784c4c5

### データの情報をを取得してみる

```bash
$ frictionless describe data/130001_tokyo_covid19_patients_per_report_date.csv # デフォルトはYAML
$ frictionless describe --markdown data/130001_tokyo_covid19_patients_per_report_date.csv # Markdownで
$ frictionless describe --help # その他オプションいろいろ
```

### データのValidationをしてみる

```bash
$ frictionless validate data/130001_tokyo_covid19_patients_per_report_date.csv
$ frictionless validate --yaml data/130001_tokyo_covid19_patients_per_report_date.csv # YAML形式で
$ frictionless validate --help # その他オプションいろいろ
```

### 変なCSVを見てみる

```bash
$ cat data/invalid.csv 
id,name,,name
1,english
1,english

2,german,1,2,3

$ frictionless extract data/invalid.csv
# ----
# data: data/invalid.csv
# ----

==  =======  ======  =====
id  name     field3  name2
==  =======  ======  =====
 1  english               
 1  english               
                          
 2  german        1      2
==  =======  ======  =====

$ frictionless describe data/invalid.csv

name: invalid
type: table
path: data/invalid.csv
scheme: file
format: csv
mediatype: text/csv
encoding: utf-8
schema:
  fields:
    - name: id
      type: integer
    - name: name
      type: string
    - name: field3
      type: integer
    - name: name2
      type: integer

$ frictionless validate data/invalid.csv

# -------
# invalid: data/invalid.csv 
# -------

## Summary 

+-----------------+------------------+
| Name            | Value            |
+=================+==================+
| File Place      | data/invalid.csv |
+-----------------+------------------+
| File Size       | 50 Bytes         |
+-----------------+------------------+
| Total Time      | 0.019 Seconds    |
+-----------------+------------------+
| Rows Checked    | 4                |
+-----------------+------------------+
| Total Errors    | 8                |
+-----------------+------------------+
| Blank Label     | 1                |
+-----------------+------------------+
| Duplicate Label | 1                |
+-----------------+------------------+
| Missing Cell    | 4                |
+-----------------+------------------+
| Blank Row       | 1                |
+-----------------+------------------+
| Extra Cell      | 1                |
+-----------------+------------------+

## Errors 

+-------+---------+-----------------+--------------------------------------------------------------------------------------+
| Row   | Field   | Type            | Message                                                                              |
+=======+=========+=================+======================================================================================+
|       | 3       | blank-label     | Label in the header in field at position "3" is blank                                |
+-------+---------+-----------------+--------------------------------------------------------------------------------------+
|       | 4       | duplicate-label | Label "name" in the header at position "4" is duplicated to a label: at position "2" |
+-------+---------+-----------------+--------------------------------------------------------------------------------------+
| 2     | 3       | missing-cell    | Row at position "2" has a missing cell in field "field3" at position "3"             |
+-------+---------+-----------------+--------------------------------------------------------------------------------------+
| 2     | 4       | missing-cell    | Row at position "2" has a missing cell in field "name2" at position "4"              |
+-------+---------+-----------------+--------------------------------------------------------------------------------------+
| 3     | 3       | missing-cell    | Row at position "3" has a missing cell in field "field3" at position "3"             |
+-------+---------+-----------------+--------------------------------------------------------------------------------------+
| 3     | 4       | missing-cell    | Row at position "3" has a missing cell in field "name2" at position "4"              |
+-------+---------+-----------------+--------------------------------------------------------------------------------------+
| 4     |         | blank-row       | Row at position "4" is completely blank                                              |
+-------+---------+-----------------+--------------------------------------------------------------------------------------+
| 5     | 5       | extra-cell      | Row at position "5" has an extra value in field at position "5"                      |
+-------+---------+-----------------+--------------------------------------------------------------------------------------+
```

## データのクリーニンフローを試してみる

### 変なデータの確認

```bash
$ cat data/countries.csv
$ frictionless describe data/countries.csv --stats # 統計情報もふくめる
$ frictionless extract data/countries.csv # なんとなく抽出はできるがなんか変
```

### スキーマの修正

```bash
$ frictionless describe data/countries.csv --yaml > countries.resource.yaml # リソース定義の作成
$ cat countries.resource.yaml
$ frictionless extract countries.resource.yaml # リソース定義ファイルからの抽出
$ frictionless validate countries.resource.yaml # バリデーションの実行
```

`countries.resource.yaml`を修正しつつスキーマを調整していく。1,2はベーシックチャレンジ、3,4はアドバンストチャレンジです。わからなところは、この辺りを見るなど。
https://specs.frictionlessdata.io/patterns/

- ヒント
    1. neighbor_id は id への外部キーなんだけどタイプが違わない？
    2. populationの型はあってるかな？
    3. どういう値が入っていたら、そのフィールドが「空」として扱うか？
        - https://specs.frictionlessdata.io/patterns/#missing-values-per-field
    4. neighbor_id が id への外部キーであることをちゃんと定義するのは？
        - https://specs.frictionlessdata.io/patterns/#table-schema-foreign-keys-to-data-packages

どうしてもわからない場合だけ、`goal`フォルダを見てね。

全部できたらあらためて、extractとvalidateをやってみましょう。（まだいくつかエラーが出ると思いますが、ここでは気にしない）

### データの修正 (transform)

上記でルールで縛れるところはだいたい対応しました。最後にデータ自体のクリーニングをしていきます。下記は、個別に修正が必要なところです。

- フランスの人口
- ドイツの隣国

データの修正は、いくつかのパイプラインステップで自動化します。パイプラインの定義は`goal`フォルダに準備されているものを使います。

```bash
$ frictionless transform data/countries.csv --pipeline goal/countries.pipeline.yaml
```

`countries.resource.yaml`の`path`と`headerRows`を書き換えて、改めてデータを確認します。

```bash
$ frictionless extract countries.resource.yaml
$ frictionless validate countries.resource.yaml
$ frictionless validate countries.resource.yaml
```

めでたしめでたし！
