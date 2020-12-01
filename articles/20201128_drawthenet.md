---
title: "システム構成図をテキストで"
emoji: "🛠"
type: "tech"
topics: ["drawthe.net", "構成図", "docker"]
published: true
---

# システム構成図をテキストで

> 💡：Gigazineさんでdrawthe.netを取り上げていたので紹介です。使い方はGigazineさんのほうが丁寧なので、気になる方はチェックしてみてください。（2020年12月1日、追記）

https://gigazine.net/news/20201201-draw-the-net/

## drawthe.netとは

[cidrblock/drawthe\.net](https://github.com/cidrblock/drawthe.net)は複雑なネットワーク図も「テキストで書いてブラウザ上でSVGレンダリングできるようにしよう」というコンセプトのもと開発されたツールです。下図のように複雑な構成図も精度高く描くことができます。

![](https://storage.googleapis.com/zenn-user-upload/lkb2e9xwzrwq9hrbgu6hx8sd1r3y)

拡大してみると情報量が多いこと、またいかに整っているかがわかると思います。
![](https://storage.googleapis.com/zenn-user-upload/u9ijcv9lxn002tai77j405jdhz9j)

デモサイトも用意されているので、サクッと試したい場合は[コチラ](http://go.drawthe.net/)が便利です。コードは[GitHubで公開](https://github.com/cidrblock/drawthe.net)されています。更新が2017年末で止まってしまっているのが玉に瑕ですが、十分な性能を発揮してくれます。

## drawthe.netを使いたい理由

美しい構成図といえばInteropを思い出しますよね。

https://www.geekpage.jp/blog/?id=2010/6/10/2

美しく整った図は人を魅了しますし、機能性にも優れています。システム構成図はさまざまな人（開発エンジニア、保守・運用エンジニア、オペレーターなど）から参照される資料なので、なるべく美しく整った状態であるべきと考えます。しかし、Visoやdraw.ioでこのような図を書こうと思うと本当に大変ですし、人の能力に依存します。また、Visoやdraw.ioで書かれた美しい構成図は、メンテナンス性が悪く、1ピクセルの配置のために常にズームインとズームアウトを繰り返すこととなります。

前書きが長くなりましたが、drawthe.netを使いたい理由は上記背景から下記3つとなります。

1. 誰でも整った図を書けるツールがほしい
2. メンテナンス性に優れたツールがほしい
3. 変更に対する差分がわかるようにしたい

これらを満たすツールがdrawthe.netだったわけです。

drawthe.netはグリッド上にアイコンを配置する記述方式をとるのが特徴的です。座標を指定するので、誰でも縦横のラインが整ったキレイな構成図を書けるというわけです。

![](https://storage.googleapis.com/zenn-user-upload/jeu2rfmg8ht8vhtmbgkykinkqf7h)
*drawthe.netはグリッド線上にアイコンを配置するから整う*

あとはテキストベースなのでGitで差分管理が容易なこと、一度ベースを作ってしまえば、汎用性があるということもありがたいポイントです。

## dockerイメージを作る

[デモサイト](http://go.drawthe.net/)でも十分使えますが、会社からだとアクセス制限がかかっていて使えない…なんてこともありますから、ローカル環境で使えるようにしたいと思います。

現代的には可搬性の高い環境構築にはdockerが使われますので、今回もdockerイメージを作ることとしました。いろいろハマりましたが、最終成果物は[oresama\-lab/drawthenet\-personal](https://github.com/oresama-lab/drawthenet-personal)に公開しました。ハマった過程が気になる方は[スクラップ](https://zenn.dev/skksky_tech/scraps/7a94bc2e3fc986557702)を見てください。

デプロイ手順も一応書いておきます。  
※最新情報は[oresama\-lab/drawthenet\-personal](https://github.com/oresama-lab/drawthenet-personal)を参照してください。

```
$ git clone https://github.com/oresama-lab/drawthenet-personal.git
$ cd drawthenet-personal
$ git clone https://github.com/cidrblock/drawthe.net.git
$ docker build --no-cache -t drawthenet:debian-nginx-node6.17.1 .
$ docker run --rm -d -p 4000:80 drawthenet:debian-nginx-node6.17.1
```

## アイコンを最新化する

`drawthe.net` のリポジトリに含まれているAWSアイコンは古いので、最新のAWSアイコンに変更します。

まずは、AWS公式が配布している `アセットパッケージ` をダウンロードして `svg` 画像を抜き出します。

参考：[AWS シンプルアイコン \- AWS アーキテクチャーセンター \| AWS](https://aws.amazon.com/jp/architecture/icons/)

AWSから落としてきたアイコンは、キレイにフォルダー分けされていますが、1階層にする必要があります。LinuxやmacOSをつかっているなら以下のコマンドで楽に `svg` ファイルを1階層に集約できます。

```
$ mkdir ~/aws-icons
$ find AWS-Architecture-Service-Icons_20200911/ -type f -name "*.svg" -exec cp {} ~/aws-icons \;
```

`svg` を抽出できたら、drawthenetリポジトリの `build/images/aws` のアイコンを置き換えます。あとは、dockerで再ビルドしてあげれば新しいアイコンを使えるようになります。drawthenetでアイコンを指定するときは拡張子なしのファイル名を使います。

| BEFORE | AFTER |
| :---: | :---: |
|![](https://storage.googleapis.com/zenn-user-upload/cckn96gw3j5ksc418bhbhrbjctt6)|![](https://storage.googleapis.com/zenn-user-upload/9cjil9xekbczkkynv16tv33hf14n)|

## 実際に使ってみる

ありがちな構成図を書いてみました。

![](https://github.com/oresama-lab/drawthenet-personal/blob/main/sample-diagrams/aws-sample-architecture.png?raw=true)
*aws-sample-architecture*

元となるYAMLファイルは以下の通りです。慣れるのに少し時間がかかると思いますが、今回作ったような図をベースに改変していけばそこまで迷うことなくテキストベースでシステム構成図を書くことができると思います。

```yaml:aws-sample-architecture.yml
diagram:
  fill: "white"
  rows: 7
  columns: 9
  gridLines: true
  margins: {top: 20, right: 20, bottom: 50, left: 20 }
title:
  author: Masatoshi Tohna
  company: oresama-lab.net
  color: black
  type: bar
  stroke: black
  logoFill: white
  logoUrl: https://3.bp.blogspot.com/-8tO27OaQmKo/W1vhDEBgLMI/AAAAAAABNtc/WqKk_bLT1k0O2loY5VH5UeNp6ejZ3GBwQCLcBGAs/s800/character_earth_chikyu_internet.png
  text: ◯◯システム 構成図
  subText: Sample Diagram for AWS
  version: 1.0.0
iconDefaults: &iconDefaults
  color: "black"
  fill: "white"
  stroke: "white"
  iconFamily: aws
  textLocation: bottomMiddle
azureEnterprise: &azureEnterprise
  color: "black"
  fill: "white"
  stroke: "white"
  iconFamily: azureEnterprise
  textLocation: bottomMiddle
cisco: &cisco
  stroke: "none"
  color: "black"
  iconFill: "darkslategrey"
  iconStroke: "lightgrey"
  iconStrokeWidth: .25
  iconFamily: "cisco"
  fill: "none"
  preserveWhite: "true"
cloud: &cloud
  <<: *cisco
  iconStroke: black
  iconStrokeWidth: 5
groupDefaults: &groupDefaults
  fill: "none"
  color: "black"
  stroke: "black"
connectionDefaults: &connectionDefaults
  color: "Black"
  stroke: "Black"
  strokeWidth: 1
connectionDashed: &connectionDashed
  color: "Black"
  stroke: "Black"
  strokeWidth: 1
  strokeDashArray: [3,3]
noteDefaults: &noteDefaults
  color: "black"
  fill: "white"
  stroke: "black"
  xAlign: center
  yAlign: center

# grouping parameters
group: &group
  - { color: "black", stroke: "black", fill: "#EEEEEE", strokeWidth: .5, textLocation: leftMiddle }
vpc: &vpc
  - { <<: *group, fill: "#DDDDDD", strokeDashArray: [3,3] }
hiddenGroup: &hiddenGroup
  - { color: "none", stroke: "none", fill: "none" }

# diagrams
icons:
  user: { <<: *azureEnterprise, text: ユーザー, icon: userenterprise, x: 1, y: 5 }
  r53-1: { <<: *iconDefaults, text: prod.example.com, icon: Res_Amazon-Route-53-Hosted-Zone_48_Light, x: "+4", y: "+1" }
  internet1: { <<: *cloud, icon: cloud, text: Internet, textLocation: center, x: "-3", y: "-1", w: 2, h: 1 }
  igw1: { <<: *iconDefaults, text: IGW, icon: Res_Amazon-VPC_Internet-Gateway_48_Light, x: "+2" }
  alb1: { <<: *iconDefaults, text: ALB, icon: Res_Elastic-Load-Balancing_Application-Load-Balancer_48_Light, x: "+1" }
  s3: { <<: *iconDefaults, text: Sorry Page, icon: Res_Amazon-Simple-Storage_Bucket-With-Objects_48_Light, y: "-2" }
  server1: { <<: *iconDefaults, text: 本番機_#1, icon: Res_Amazon-EC2_Instance_48_Light, x: "+1", y: "+2" }
  db1:  { <<: *iconDefaults, text: DB(Master), icon: Res_Amazon-EC2_DB-Instance_48_Light, x: "+1" }
  server2: { <<: *iconDefaults, text: 本番機_#2, icon: Res_Amazon-EC2_Instance_48_Light, x: "-1", y: "-1" }
  db2:  { <<: *iconDefaults, text: DB(slave), icon: Res_Amazon-EC2_DB-Instance_48_Light, x: "+1" }

  r53-2: { <<: *iconDefaults, text: dev.example.com, icon: Res_Amazon-Route-53-Hosted-Zone_48_Light, x: 5, y: 0 }
  user2: { <<: *azureEnterprise, text: Administrator, icon: userpermissions, x: 1, y: 1 }
  internet2: { <<: *cloud, icon: cloud, text: Internet, textLocation: center, x: "+1", w: 2, h: 1 }
  igw2: { <<: *iconDefaults, text: IGW, icon: Res_Amazon-VPC_Internet-Gateway_48_Light, x: "+2" }
  server3: { <<: *iconDefaults, text: 開発機, icon: Res_Amazon-EC2_Instance_48_Light, x: "+2" }
  db3:  { <<: *iconDefaults, text: DB(Master), icon: Res_Amazon-EC2_DB-Instance_48_Light, x: "+1" }

groups:
  vpc1: { <<: *groupDefaults, name: VPC, members: [igw1, alb1, az1, az2] }
  vpc2: { <<: *groupDefaults, name: VPC, members: [igw2, az3] }
  az1: { <<: *groupDefaults, name: AZ-a, members: [server1, db1], textLocation: topLeft }
  az2: { <<: *groupDefaults, name: AZ-c, members: [server2, db2], textLocation: bottomLeft }
  az3: { <<: *groupDefaults, name: AZ-a, members: [server3, db3], textLocation: topLeft }
connections:
  - { <<: *connectionDefaults, curve: curveStepBefore, endpoints: [user, r53-1:名前解決] }
  - { <<: *connectionDefaults, endpoints: [user, internet1] }
  - { <<: *connectionDefaults, endpoints: [internet1, igw1] }
  - { <<: *connectionDefaults, endpoints: [igw1, alb1] }
  - { <<: *connectionDefaults, endpoints: [alb1, server1] }
  - { <<: *connectionDefaults, endpoints: [alb1, server2] }
  - { <<: *connectionDefaults, endpoints: [server1, db1] }
  - { <<: *connectionDefaults, endpoints: [server2, db1] }
  - { <<: *connectionDashed, endpoints: [server2, db2] }
  - { <<: *connectionDashed, endpoints: [r53-1:ヘルスチェック, alb1] }
  - { <<: *connectionDashed, curve: curveStepBefore, endpoints: [alb1, s3:フェールオーバー] }
  - { <<: *connectionDefaults, endpoints: [user2, internet1] }
  - { <<: *connectionDefaults, endpoints: [user2, internet2] }
  - { <<: *connectionDefaults, curve: curveStepBefore, endpoints: [user2, r53-2] }
  - { <<: *connectionDefaults, endpoints: [internet2, igw2] }
  - { <<: *connectionDefaults, endpoints: [igw2, server3] }
  - { <<: *connectionDefaults, endpoints: [server3, db3] }
```

良かったらみなさんも使ってみてください。そして能力のある人はぜひ[cidrblock/drawthe\.net](https://github.com/cidrblock/drawthe.net)にプルリク出してより使えるツールにしていってください。

以上