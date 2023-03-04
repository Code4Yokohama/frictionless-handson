# Frictionless Data Hands On

## GitPodの起動

下記をブラウザーで開くとGitPodの環境が立ち上がります。
https://gitpod.io/#https://github.com/Code4Yokohama/frictionless-handson

## frictionlessモジュールのインストール

```bash
$ pipenv sync
```

必要なpythonのバージョンが未インストールの場合には、インストールするかどうか聞いてくるので、Yesを選択しましょう。

## 仮想環境に入る

```bash
$ pipenv shell
Launching subshell in virtual environment...
 . /workspace/frictionless-handson/.venv/bin/activate
gitpod /workspace/frictionless-handson (main) $  . /workspace/frictionless-handson/.venv/bin/activate
(frictionless-handson) gitpod /workspace/frictionless-handson (main) $ 
```

プロンプトの先頭に (frictionless-handson) が表示されていれば仮想環境に入っている。抜けたい場合には exit する。

## frictionless コマンドが使えることを確認

```bash
(frictionless-handson) gitpod /workspace/frictionless-handson (main) $ frictionless --version
5.8.1
```

WIP...
