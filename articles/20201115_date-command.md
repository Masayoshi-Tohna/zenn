---
title: "dateコマンドの使い方"
emoji: "🗓"
type: "tech"
topics: ["linux", "bash", "shell script", "command"]
published: true
---

# date

[GNU date](https://www.gnu.org/software/coreutils/manual/html_node/Examples-of-date.html)コマンドに再入門したのでメモとして残しておきます。

## 検証環境

Ubuntu 18.04.4 LTSで動作確認しています。

```sh
$ cat /etc/*-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=18.04
DISTRIB_CODENAME=bionic
DISTRIB_DESCRIPTION="Ubuntu 18.04.4 LTS"
NAME="Ubuntu"
VERSION="18.04.4 LTS (Bionic Beaver)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 18.04.4 LTS"
VERSION_ID="18.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=bionic
UBUNTU_CODENAME=bionic
```

## 基本

`date` は日付を返すコマンドです。

```sh
$ date
Sat Nov 13 10:08:42 JST 2020
```

`+` 以降にフォーマット文字列を指定することで日時を整形できます。

```sh
$ date '+%Y-%m-%d'
2020-11-13
```

時刻を得るには `%H:%M:%S` で整形します。

```sh
$ date +"%Y-%m-%d %H:%M:%S"
2020-11-13 10:08:42
```

## 過去の日付を得る

### 1日前の日付を得る

`--date '1 day ago'` で1日前の日付を得ることができます。

```sh
$ date '+%Y-%m-%d' --date '1 day ago'
2020-11-12
```

`--yesterday` でも同様の結果が得られます。

```sh
$ date '+%Y-%m-%d' --date 'yesterday'
2020-11-13
```

### 1週間前の日付を得る

```sh
$ date '+%Y-%m-%d' --date '1 week ago'
2020-11-06
```

### 1か月前の日付を得る

```sh
$ date '+%Y-%m-%d' --date '1 month ago'
2020-10-13
```

### 1年前の日付を得る

```sh
$ date '+%Y-%m-%d' --date '1 year ago'                   
2019-11-13
```

### 任意の日数前の日付を得る

`now` のあとに任意の日数を減算することで任意の日数前の日付を得られます。

```sh
$ date '+%Y-%m-%d' --date 'now - 3 days'                             
2020-11-10
```

`weeks` も使えます。

```sh
$ date '+%Y-%m-%d' --date 'now - 3 weeks'
2020-10-23
```

`years` も使えます。

```sh
$ $ date '+%Y-%m-%d' --date 'now - 3 years'
2017-11-13
```

`now` を任意の日付とすることもできます。

```sh
$ date '+%Y-%m-%d' --date '1970-1-1 - 3 years'
1967-01-01
```

加算する日数は複数指定できます。

```sh
$ date '+%Y-%m-%d' --date '1970-1-1 - 3 years 3 month 3 days'
1967-04-03
```

## 未来の日付を得る

### 1日後の日付を得る

```sh
$ date '+%Y-%m-%d' --date 'tomorrow'
2020-11-15
```

### 1週間後の日付を得る

```sh
$ date '+%Y-%m-%d' --date '1 week'
2020-11-20
```

### 1か月後の日付を得る

```sh
$ date '+%Y-%m-%d' --date '1 month'
2020-12-13
```

### 1年後の日付を得る

```sh
$ date '+%Y-%m-%d' --date '1 year'
2021-11-13
```

### 任意の日数後の日付を得る

`now` のあとに任意の日数を加算することで任意の日数後の日付を得られます。

```sh
$ date '+%Y-%m-%d' --date 'now + 3 days'                             
2020-11-13
```

`weeks` も使えます。

```sh
$ date '+%Y-%m-%d' --date 'now + 3 weeks'
2020-12-04
```

`years` も使えます。

```sh
$ date '+%Y-%m-%d' --date 'now + 3 years'
2023-11-13
```

`now` を任意の日付とすることもできます。

```sh
$ date '+%Y-%m-%d' --date '1970-1-1 + 3 years'
1973-01-01
```

加算する日数は複数指定できます。

```sh
$ date '+%Y-%m-%d' --date '1970-1-1 + 3 years 3 month 3 days'
1973-04-04
```

## 任意の日付を得る

### 月初の日付を得る

```sh
date '+%Y-%m-01' --date '1 month'
2020-12-01
```

### 月末の日付を得る

月末を取得したい月の次月1日の前日を計算すればOKです。

```sh
date '+%Y-%m-%d' --date "1 day ago $(date '+%Y-%m-01' --date '2020-03-01')"
```

先月末の日付を取得する場合は以下ワンライナーを使用できます。

```sh
$ date --date="$(date +"%d") days ago" +"%Y-%m-%d"
2020-10-31
```
## 日付の差を得る

### `expr` コマンドによる実現

`date` コマンドのみでは実現できないので `expr` コマンドを使って計算します。簡易なシェルスクリプトを用意すればOK。下記例では `+"%s"` でUNIX秒に変換した上で差分を計算しています。

```sh
#!/bin/bash
date1=$(date --date $1 +"%s")
date2=$(date --date $2 +"%s")

function calc_date() {
        expr \( $date2 - $date1 \) / 60 / 60 / 24
}

function show_usage() {
        echo "Usage: basename $0 <start-date> <end-date>"
        exit 1
}

if [ $# -ne 2 ]; then
        show_usage
fi

calc_date
```

実行すると日付の差分が表示されます。

```sh
$ chmod +x ./diffdate.sh
$ ./diffdate.sh 2020-1-1 2020-11-13
317
```

`today` のような `date` コマンドで解釈できる自然言語も使用できます。

```sh
$ ./diffdate.sh 2020-1-1 today
317
```

### `datediff` コマンドによる実現

`dateutils` には `datediff` コマンドが含まれており、簡単に日付の差分を計算できます。`dateutils` をインストール可能な環境ではこちらがオススメ。

`dateutils` のプロジェクトページは[コチラ](http://www.fresse.org/dateutils/)。

以下、インストール手順です（出力は省略）。

```sh
$ sudo apt install autoconf libtool-bin make
$ wget https://bitbucket.org/hroptatyr/dateutils/downloads/dateutils-0.4.7.tar.xz
$ tar Jxfv dateutils-0.4.7.tar.xz 
$ cd dateutils-0.4.7/
$ autoreconf -i
$ ./configure
$ sudo make install
```

インストールが完了したことは以下コマンドで確認できます。

```sh
$ whereis datediff
datediff: /usr/local/bin/datediff
$ datediff --version
datediff 0.4.7
```

引数に差分を計算したい日付を入れて実行すれば差分の日数が表示されます。

```sh
$ datediff 2020-1-1 2020-11-13
317
```

`datediff` コマンドでも、`date` コマンドで解釈可能な自然言語を使用できます。

```sh
$ datediff 2020-1-1 today
317
```

以上