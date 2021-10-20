# セクション 2

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

ドッカーイメージの履歴を見る

```
docker history nginx
```

docker イメージを削除する

```
docker rmi nginx
```

## Docker コマンドを使う。

- nginx サーバのコンテナを起動

```
docker run -p 80:80 nginx
```

- -p:--publish の略
- 80:80 　：＜ホスト側＞：＜コンテナ側＞
  - コンテナ側は８０にしていることが多い

* 起動中のコンテナの一覧を表示

```
docker ps
# ps=process status
```

- コンテナの停止

```
docker stop ＜コンテナのID＞
```

- 停止中のコンテナも含めてすべて一覧表示

```
docker ps --all
```

- 停止中のコンテナを削除

```
docker rm ＜コンテナのID＞
```

- コンテナをバックグラウンドで起動する(Docker を起動しつつターミナルで入力を受け付ける)

```
docker run -p 80:80 --detach nginx
```

- ログを表示

```
docker logs nginx
```

### 上級編（ちょいむず）

- 作業中のコンテナの中にシェルで入る。（サーバの中に入るみたいな感覚）

```
docker exec -it nginx bash
# docker exec --interactive --tty nginx bash
# docker exec --interactive --tty ＜コンテナの名前＞ ＜shとかbashとか＞
```

- 改めて停止

```
docker stop nginx01
# docker stop ＜コンテナのname＞
```

- ホストからコンテナへ Volume をマウント

ホスト：自分の PC、ターミナル

ローカルにあるファイルをコンテナへアップするみたいなノリ

```
docker run --volume HOST:CONTAINER
# docker run --volume index.html:/home nginx
# docker run --volume ＜ローカルにあるファイル＞:＜コンテナにマウントするディレクトリ＞ ＜コンテナ名（自分でつけたものではなくpullしてきてきたときに使う名前）＞
```

コンテナ内での変更はローカルにも反映される的な説明をしていた。

- コンテナの再起動

```
docker container start nginx01
# docker container start ＜コンテナの名前＞
```

- コンテナ内へ環境変数を追加

```
docker run -env KEY=VALUE nginx
```

- 実際に追加

```
docker run --env TEST_ENV=hellow_world -d --name nginx_env nginx
docker exec -it nginx_env sh
```

これでコンテナの中に入れたのでコンテナ内で環境変数が設定されているかを見る。

```
env
#output: TEST_ENV=hellow_world
```

- 使ったコマンドの検索:Control + r で一覧が出てきて Enter を押すとそのコマンドを実行できる。

* コンテナのメタ情報を表示する。

```
docker inspect nginx
# docker inspect ＜コンテナ名でもイメージ名でも行けるっぽい（詳細不明）＞
```

### ホストからコンテナにマウントする

- nginx のフォルダに検索をかける

```sh
find / type d -name nginx
# ルートからnginxというディレクトリがあるか検索する
```

- 上のコマンドのアウトプット

```sh
find: '/proc/24/map_files': Permission denied
find: '/proc/25/map_files': Permission denied
find: '/proc/26/map_files': Permission denied
find: '/proc/27/map_files': Permission denied
/usr/share/doc/nginx
/usr/share/nginx
/usr/lib/nginx
/etc/nginx
/var/cache/nginx
/var/log/nginx
```

このアウトプットを引数として grep に渡すときはパイプを使う。

```
find / type d -name nginx | xargs grep -r "html"
```

アウトプットの一部は以下のようになる。

```
/etc/nginx/conf.d/default.conf:        root   /usr/share/nginx/html;
```

HTML の配置場所がわかったので cat コマンドで index.html の中身を見る

```
cat /usr/share/nginx/html/index.html
```

これを"hello world"に上書きする。

```
echo "Hello World" > /usr/share/nginx/html/index.html
```

### ホストからコンテナにマウントする

ホストの HTML ファイルを書き換える。

```
echo "Hello World From Host" > index.html
```

マウントする

```sh
docker run -d --volume "$(pwd)":/usr/share/nginx/html -p 80:80 --rm --name nginx01 nginx
# $(pwd):/usr/share/nginx/html はホストの現在のディレクトリにあるファイルたちをコンテナの/htmlフォルダにマウントするという意味
# --rmはコンテナ終了時にコンテナを自動的に削除するコマンド
# --nameは自分の好きな名前をつけられる
# 最後のnginxはイメージ名
```

# セクション 6

Nginx コンテナを上書きして新たに「Hello World イメージ」を作成する。

- 起動中の Nginx コンテナにシェルで入り HTML ファイルを上書きする

```
# コンテナ作成
docker run -d -p 80:80 --rm --name nginx02 nginx
# コンテナに入る
docker exec -it nginx02 bash
# HTMLファイルを上書き
echo "hello world" > /usr/share/nginx/html/index.html
```

- 起動中のコンテナから"NEW IMAGE"イメージを作成

```
docker commit nginx02 new_image
# docker commit ＜コンテナ名＞ ＜新しく作成するイメージ名＞
```

- NEW IMAGE イメージからコンテナを起動して確認

```
# 作成したイメージを検索する
docker images | grep new
# -dはデタッチドモード（バックグラウンドで起動してターミナルに入力をできるようにする）
docker run -d -p 8080:80 --name new_container new_image
```

- 中身を確認する

```
curl localhost:8080
```

編集した hello world は返ってこない。Docker Volume でマウントされたファイルはコンテナ内に保存されないから。

保存するやり方を見る。

1. ボリュームをマウントせずに`docker run`する。
2. コンテナ内に入る
3. HTML ファイルを上書きする
4. イメージを作成する(`docker commit`)
5. イメージからコンテナを起動(`docker run`)

```sh
docker run -p 80:80 --rm --name nginx01 -d nginx
docker exec -it nginx01 bash
## コンテナ内始まり
cat /usr/share/nginx/html/index.html  # 中身がデフォルトであることを確認
echo "Hello World from container" > /usr/share/nginx/html/index.html # HTMLファイル上書き
exit
## コンテナ内終わり
curl localhost:80 # output:Hello World from container
docker commit nginx01 hello_world_from_container
# docker commit ＜既存のコンテナ名＞ ＜新しいイメージ名＞
docker images | grep hello # 作ったイメージを確認（hello_world_from_container
が出る。）
docker run -d -p 8080:80 --name new_container hello_world_from_container
# docker run -d -p 8080:80 --name ＜新しいコンテナ名＞ ＜元になるイメージ名＞
```

## イメージをカスタマイズして自分のイメージを作る

apt:linux のパッケージマネージャー

- Dockerfile でレシピの作成

```
# ベースとなるイメージ
FROM nginx:latest
# コンテナでの作業するパス。下のディレクトリ上にindex.htmlが作成される。
WORKDIR /usr/share/nginx/html
# --volumeと一緒で左がホスト、右側がコンテナを表す。左を右にコピーする。
COPY index.html index.html

# curl コマンドをインストールする
RUN apt update && api install -y curl
```

- Dockerfile からイメージを作成

```
docker build --tag dockerfile_hello .
# docker build --tag ＜つけたい名前＞ ＜dockerfileがある場所＞
```

- 自作イメージからコンテナを起動

```
docker run -d -p 9090:80 --name hello_v2 docker_hello
# docker run -d -p 9090:80 --name ＜任意のコンテナ名＞ ＜先ほど作成したイメージ名＞
```

## Dockerfile

Dockerfile：UML ダイアグラムみたいなもの
イメージ：クラス
コンテナ：オブジェクト

-　 dockerfile

```Dockerfile
FROM nginx:latest
WORKDIR /usr/share/nginx/html
COPY index.html index.html
RUN apt update && api install -y curl

# 環境変数の設定
ENV env_key env_value
# nodeコンテナの場合だと["node"]と入れる。
CMD ["nginx","-g","daemon","off"]
```

### 自作イメージをリポで公開

ドッカーイメージを作成したらタグ付をして DockerHub などのコンテナストレージにプッシュします。

- 作成したイメージをタグ付けする

```sh
docker tag DOCKER_HUB_USERNAME/REPO_NAME
# docker tag dockerfile_hollo mkt1771/dockerfile_hello_world_nginx
```

- Docker Hub にログイン

```sh
docker login
```

- DockerHub の自分のリポジトリにイメージをアップロード

```sh
docker push DOCKER_HUB_USERNAME/REPO_NAME
```

### コンテナ・イメージのクリーンアップ

- 停止中のコンテナと無名のイメージを削除

```sh
docker system prune
```

- 起動中のコンテナを**全コンテナ**を削除

```sh
docker rm -vf $(docker ps -a -q)
# -vf: --volumes(コンテナと関連付けられたボリュームを削除) と --forceの省略形
# -q: --quiet省略形：整数のIDのみを出力する
```

- **すべての**イメージを削除

```sh
docker rmi -f $(docker images -a -q)
```

# セクション 7

## Docker compose

複数のコンテナを同時に起動したい場合に`docker-compose`を使う。

バックグラウンドで起動するには`-d`をつける。

```sh
docker-compose -f docker-compose.yaml up -d
```

コンテナの停止と削除をするときは down を使う。

```bash
docker-compose -f docker-compose.yaml down
```

ログを見るときは

```
docker-compose -f docker-compose.yaml logs -f
```

### docker-compose でコンテナレプリカを複数起動

複数起動するときは yaml から

- `container_name`を削除する。
- `ports`を範囲指定する。

* docker-compose.replicas.yaml

```yaml
services:
  nginx:
    image: nginx:latest
    # container_name: docker_compose_nginx #これを削除する。
    environment:
      - env_key=env_value
    # ports: - "80:80"としていた。
    ports:
      - "80-90:80"
    volumes:
      - ${PWD}:/usr/share/nginx/html:ro
    # roはread only
  node:
  ~~~~
```

- `--scale`で nginx のコンテナを３つ作成する。

```sh
docker-compose -f docker-compose.replicas.yaml up --scale nginx=3
```

ただし curl はレプリカを作成できない。なぜなら以下のようにコンテナ名(`container_name`)を指定してあるから

```yml
curl:
  image: curlimages/curl:7.68.0
  container_name: docker_compose_curl
  command: ["sleep", "500"]
  ports:
    - "8080:8080"
```

### コンテナ同士のネットワーク

docker-compose で定義されたコンテナはコンテナ同士で通信ができる。

- docker-compose.yaml で docker-compose コマンドを実行する。

```bash
docker-compose -f docker-compose.yaml up -d
```

- Ubuntu コンテナに入る

```sh
docker exec -it docker_compose_ubuntu bash
```

- curl コマンドをインストール

```sh
apt update && apt install -y curl
```

- Ubuntu から Nginx に通信ができるか確認

```sh
curl docker_compose_nginx:80 # output:hello world
# curl ＜container_name＞:＜ポート＞
```

#### docker run で起動したコンテナから nginx にアクセスできるかやってみる。

つながらない。

```sh
docker run -it --rm curlimages/curl:7.68.0 sh
curl docker_compose_nginx:80
# output:curl: (6) Could not resolve host: docker_compose_nginx
```

# セクション 9

## 4 つの Docker ネットワークモード

全然わからん。

参考：https://qiita.com/TsutomuNakamura/items/ed046ee21caca4a2ffd9

- bridge ネットワーク
  ホスト（我々の PC）から IP アドレスが振り分けられるのでホストとコンテナの IP アドレスの範囲は異なる。

`docker run`でネットワークを指定しなければデフォルトで bridge ネットワークが作られる。

- host ネットワーク

8080:80(host:container)のように IP を対応付けることはできない。（我々の PC から IP アドレスが振り分けられていないので）

- none ネットワーク
  コンテナは Bridge にも Host ネットワークにもリンクされず IP アドレスが無いので接続不可になる。

- overlay ネットワーク
  k8s で使われるっぽい

### ネットワークの表示

```sh
docker network ls
```

### ネットワークの作成

```sh
docker network create --driver bridge custom_bridge
# docker network create --driver ＜bridge|none|host|overlay＞ ＜ネットワークの名前＞
```

docker run 時にネットワークを指定するには`--network=＜作成したネットワーク名＞`とする。

```sh
docker run --network=none --rm -d --name none -p 80:80 nginx
```

### ネットワークの削除

```
docker network rm ＜ネットワークの名前＞
```

# セクション 10

Docker Volume のマウント

Docker のストレージはコンテナ内ではなくホスト上に保存する。
`docker run -volume /host/dir/:/container/dir`みたいにマッピングして Volume をマウントしたよな。

コンテナ上の Volume を変更するとホスト上のファイルも変更される。

- `docker run`コマンドで`--volume`でマウントする。その後 Ubuntu コンテナに入りファイルを作成する。

```sh
docker run -d --volume $(pwd):/home --name test --rm -it busybox sleep 50
# -i:--interactive ：ターミナルで引き続きコマンドが打てるようになる(コンテナ内の標準出力(コンテナの中のターミナルのログみたいな)とホスト（コンソール）の標準出力をつなげる)
# -t:--tty(tele type writerの略)　：コンテナで擬似的なターミナルを作る（新しいコンテナの中に疑似ターミナル (pseudo-tty) を割り当てる）
## http://docs.docker.jp/v1.10/engine/userguide/containers/dockerizing.html

# コンテナに入る
docker exec -it test sh

# ここからコンテナ内で動く
cd home/
# コンテナ内のテキストファイルを上書き
echo "hi from container" > from_container.txt
exit

# コンテナ内で書き換えた内容がホストにも反映されているか確認
cat from_container.txt
# output : hi from container
```

# セクション 11 コンテナオーケストレーション

コンテナオーケストレーション：複数コンテナを複数ホスト（サーバー）に割り当てること

## Docker Swarm と Kubernetes の違い

Swarm は実戦向きでない。
どちらも Single Host から Multi Host にしてホストがダウンしたときのダウンタイムを減らすのが目的。

## Docker Swarm のコマンド

- Docker Swarm の初期化

```sh
docker info # SwarmのStatusをチェック
docker swarm init
docker node ls # SwarmのNodeをリストアップ
```

- Docker Swarm の Service を起動

```sh
docker service create --name helloworld alpine:3.10 ping docker.com
# docker service create --name ＜好きな名前＞ alpine:3.10 ping
docker service ls
docker service inspect --pretty helloworld
# --prettyで整形して表示する
```

- Docker Swarm の Service をスケールアップ

```sh
docker service scale helloworld=3
#docker service scale ＜docker service createで指定した名前＞=3
```

- Service をローリングアップデート

```sh
docker service update --image alpine:3.11 helloworld
# docker service update --image ＜イメージ:バージョン＞ ＜作成したサービスの名前＞
```

- Service の削除

```sh
docker service rm ＜サービスの名前＞
```

## Kubernetes のデモ

Minikube を起動してクラスタを作成

```sh
minikube start
```

エラーが出た

```
❌  Exiting due to GUEST_PROVISION: Failed to start host: driver start: Error setting up host only network on machine start: /usr/local/bin/VBoxManage hostonlyif ipconfig vboxnet3 --ip 192.168.99.1 --netmask 255.255.255.0 failed:
VBoxManage: error: Code E_ACCESSDENIED (0x80070005) - Access denied (extended info not available)
VBoxManage: error: Context: "EnableStaticIPConfig(Bstr(pszIp).raw(), Bstr(pszNetmask).raw())" at line 242 of file VBoxManageHostonly.cpp
```

https://128mots.com/index.php/en/2021/01/31/exiting-due-to-guest_provision/

これを見て

```
minikube delete
minikube start --driver=docker
```

で問題なさそう？
