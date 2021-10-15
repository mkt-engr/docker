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
