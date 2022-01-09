# docker-compose関連.md
docker-compose.ymlについて記載してます。
これをみてdocker-compose.ymlを作るぞー！ってよりかは既存のdocker-compose.ymlどっかから引っ張ってきて、
これってどういう意味だろう。。ってときにここを参照してもらった方がいいかなと思います。
上から下まで全ての項目舐める必要はないです。
意外とネットで調べても全然ほしい回答もらえなかったりするし、公式ドキュメントも読む気失せるんでここにある程度まとめとこうかなって思います。

## docker-composeとは
複数コンテナの起動設定ファイル。
Dockerfileと考え方はほぼ同じで、docker runでいちいちオプションつけてコマンドうつの面倒くさいから、
ファイルにまとめて書いちゃおうっていう考えです。
ただ、Dockerfileとの違いは複数のコンテナの設定をひとつのファイルにまとめて記載できてしまうという点です。
ネットワークの設定(IPアドレスなどなど)も可能です。
docker-compose.ymlというyamlファイルを作成してそこに設定を書き込めば、
```
docker-compose up -d
```
でdocker-compose.ymlに記載された設定でコンテナ群が起動します。
終了時は
```
docker-compose down
```
できれいにしてくれます。

### docker-compose.ymlの項目
docker-compose.ymlの項目を簡単に記載します。
僕の目に触れたものだけ書いてるんで全てではないです。
あと、公式のdocker-composeマニュアルを和訳してくれている神がいたんでurl共有します。
https://zenn.dev/goforbroke/articles/a041488b6e548c886535

* version
バージョン情報。おそらくバージョンによって対応していない項目がある(多分。。)と思うので一応気をつけてください。
* services
各コンテナの設定情報はservices配下に階層構造で記載されます。
各service名はコンテナ名とは異なるので気をつけてください。
一般的にはコンテナ名と同じにしている気がします。
* image
使用するイメージ。ローカルにイメージがなければ、
どっかのdockerリポジトリからインターネット経由で引っ張ってくれる。
* container_name
コンテナ名。
* cap_add
権限の設定。
ALLだと全て。これ以外使ったことない。ほかは調べてちょ。
https://linuxjm.osdn.jp/html/LDP_man-pages/man7/capabilities.7.html
* build
この項目に関しては2種類の書き方がある。
(おそらくバージョンの違い？どっかのサイトでは書き方2はversion3.8で記載されていた。基本的に書き方1で書いてたけど2のほうがベターらしい。)
1. Dockerfileのパスを指定。
2. 以下のようにcontextとdockerfileに項目を分ける。
```
build:
context:.
dockerfile:./Dockerfileへのパス/Dockerfile
```
contextを使用する理由は以下サイトを見るとわかりやすい気もしなかったりしたり。
https://qiita.com/sam8helloworld/items/e7fffa9afc82aea68a7a
ざっくりまとめると、
contextでどこまで上の階層のファイルを読み込めるようにするか設定している?
* volumes
マウントの設定。
コンテナのディレクトリをホスト側のディレクトリにマウントします。
マウントは、コンテナ側のディレクトリとホスト側のディレクトリを一緒にするイメージです。
ちょっと説明しづらいんで調べてみてください。マウントはDockerに限った話ではなく、一般的な用語です。
https://amateur-engineer-blog.com/docer-compose-volumes/
このサイトとかわかりやすいです。
volumesにも色々な書き方があるらしいです。まとめる暇があったらまとめときます。
* ports
ホスト側のポートとコンテナのポートをつなげています。(ポートフォワーディング)
左側がホスト側のポート番号。右側がコンテナ側のポート番号です。
webブラウザ上でlocalhost:8000にアクセスすると、コンテナの8000番にポートフォワーディングされます。
* hostname
ユーザー名。
docker exec -it コンテナ名 bashで入ったときのコンソール見てみてください。
多分設定したユーザー名でログインされているはずです。
* command
起動時にdockerコンテナで実行するコマンド。
確かDockerfileにCMDを設定してあった場合、そのCMDは実行されず、docker-compose.ymlのcommandが実行されます。
* tty
trueかfalseのブーリアン。
https://zenn.dev/hohner/articles/43a0da20181d34
コンテナ継続起動させたいならtrueにするらしい。正直よくわからん。
実験してみたら確かにtrueにしておかないとコンテナすぐ終了する。
多分、仮想端末をぽっとフォアグラウンドで用意してあげると、その端末を閉じない限り永遠にコンテナが終了しないみたいなイメージ？
とりあえずtrueで設定しておけば起動し続けるんだな〜って感じ。

