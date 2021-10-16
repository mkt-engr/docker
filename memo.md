# セクション３

## Docker Playground

[Docker Playground](https://labs.play-with-docker.com/)

# セクション３

## シェル基本コマンド

### プロセス

```linux
ps
```

## サーチ

- ファイル・フォルダーを探す find

```
find / -type d -name nginx
#find ＜場所＞ -type ＜ディレクトリd or ファイルf＞ -name ＜検索ワード＞
```

- 文字を検索 grep

```
grep "Hello" test.txt -n
# -nは--numberで何行目にあったかを表示する
```

- フォルダー名を探し、その中のファイルを文字検索

パイプ(`|`)はパイプの左側のコマンドの返り値をパイプの右側に渡す。

```
find / -type d -name nginx | xargs grep -r html
# -r はrecursiveの略
```

- ファイル作成

```
touch index.txt
```

- ファイルに文字書く

```
# 追記(>が２つ)
echo "＜追記したい文字列＞" >> index.txt
# 上書き（>が一つ）
echo "＜追記したい文字列＞" > index.txt
```

- --help よりも詳しくコマンドを知りたい

```
man ls
```

# セクション 3

docker のイメージを取得

```
docker pull nginx
```

watch コマンドは定期的に入力したコマンドを打ってくれる。
watch コマンドがなかったので brew でインストールする。

```
brew install watch
```

```
watch -t "docker ps"
```

これで２秒毎に`docker ps`コマンドを実行してくれる

```
docker images
```

でダウンロードされたイメージを見る。

履歴を見る
