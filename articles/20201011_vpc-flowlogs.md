---
title: "AWS VPC FlowLogsの挙動を確かめてみた"
emoji: "🧪"
type: "tech"
topics: ["aws", "vpc floe logs", "cloud watch logs"]
published: true
---

## はじめに

AWSの[VPC フローログに関するドキュメント](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/flow-logs.html)を読んでいると `start` フィールドと `end` フィールドについて気になる記述を見つけました。いまいち挙動のイメージがわかなかったので、実際に動かして確認してみました。

<table>
	<tr>
		<td>フィールド</td>
		<td>説明</td>
		<td>バージョン</td>
	</tr>
	<tr>
		<td>start</td>
		<td>集約間隔内にフローの最初のパケットが受信された時間 (UNIX 秒)。<b>これは、パケットがネットワークインターフェイス上で送信または受信されてから最大 60 秒になる場合があります。</b></td>
		<td>2</td>
	</tr>
	<tr>
		<td>end</td>
		<td>集約間隔内にフローの最後のパケットが受信された時間 (UNIX 秒)。<b>これは、パケットがネットワークインターフェイス上で送信または受信されてから最大 60 秒になる場合があります。</b></td>
		<td>2</td>
	</tr>
</table>

▲ VPC FlowLogsで使用可能なフィールド

## 確認のために使用した構成

AWS上にVPC FlowLogsを有効にしたVPCを作成し、EC2インスタンスを1台建てました。セキュリティグループはSSH(tcp/22)とICMPのみを許可しました。VPC FlowLogsはCloudWatch Logsに送ることでログの書き出し状況を見やすくし、最終的には当たりをつけたデータをUnix Timeの変換や分析のためにCSV形式でエクスポートし、加工しました。

![](https://storage.googleapis.com/zenn-user-upload/w4aalll92iqnh3yjauadcod6jxy4)

## VPC FlowLogsの仕様

AWSのVPC FlowLogsは、1分または10分のAggregation Intervalを指定し、そのインターバル内に対象のインターフェイスを通過したパケットを記録しています。

**The aggregation interval**

> the period of time during which a particular flow is captured and aggregated into a flow log record. ~the maximum aggregation interval is 10 minutes. When you create a flow log, you can optionally specify a maximum aggregation interval of 1 minute.

引用：[VPC Flow Logs \- Amazon Virtual Private Cloud](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)

この間に何パケット通過しようとFlowLogs上は1レコードとして処理されます。ただし、通過する方向によって別のパケットとして扱われるので、たとえばpingを送ったとするとICMP ECHO REQUESTとICMP ECHO REPLYのパケットが記録されるレコードは別のレコードとなるようです。

今回は冒頭に書いたとおり、「これは、パケットがネットワークインターフェイス上で送信または受信されてから最大60秒になる場合があります。」という挙動が実際どのようなものなのか見てみます。

## 観測結果

:::message
ここから先、取得したレコードを表形式で記載しますが、アスタリスク（*）つきの項目はわかりやすくするために筆者が追記した項目となりますのでご注意ください。
:::

まずは連続したパケットを送りつけた結果です。Aggregation interval通り、1レコードは59秒（=約1分）の経過時間となっており、その間に85パケットが通過したことになっています。

フィールド | 値
-- | --
timestamp | 1602397806
*timestamp | 2020/10/11(日)   15:30:06
version | 2
account-id | xxxxxxxxxxxx
interface-id | eni-0ff9b2e1f8edb8688
srcaddr | xxx.xxx.xxx.xxx
dstaddr | 10.255.1.22
srcport | 52233
dstport | 22
protocol | 6
packets | **85**
bytes | 8216
start(unixtime) | 1602397806
*start | 2020/10/11(日)   15:30:06
end(unixtime) | 1602397865
*end | 2020/10/11(日)   15:31:05
*経過時間 | **0:00:59**
action | ACCEPT
log-status | OK

一方で、1パケットしか通過しなくても1レコードが59秒（=約1分）のレコードも存在しました。これが、ドキュメントに記載されている「パケットがネットワークインターフェイス上で送信または受信されてから最大60秒になる場合がある」という事象のようです。

フィールド | 値
-- | --
timestamp | 1602397446
*timestamp | 2020/10/11(日)   15:24:06
version | 2
account-id | xxxxxxxxxxxx
interface-id | eni-0ff9b2e1f8edb8688
srcaddr | xxx.xxx.xxx.xxx
dstaddr | 10.255.1.22
srcport | 52233
dstport | 22
protocol | 6
packets | 1
bytes | 136
start(unixtime) | 1602397446
*start | 2020/10/11(日)   15:24:06
end(unixtime) | 1602397505
*end | 2020/10/11(日)   15:25:05
*経過時間 | 0:00:59
action | ACCEPT
log-status | OK

「最大60秒」と言っていることからわかるように、以下の例では経過時間が39秒となっており、必ずしも経過時間が1分となるわけではないようです。

フィールド | 値
-- | --
timestamp | 1602397706
*timestamp | 2020/10/11(日)   15:28:26
version | 2
account-id | xxxxxxxxxxxx
interface-id | eni-0ff9b2e1f8edb8688
srcaddr | xxx.xxx.xxx.xxx
dstaddr | 10.255.1.22
srcport | 52233
dstport | 22
protocol | 6
packets | 1
bytes | 136
start(unixtime) | 1602397706
*start | 2020/10/11(日)   15:28:26
end(unixtime) | 1602397745
*end | 2020/10/11(日)   15:29:05
*経過時間 | 0:00:39
action | ACCEPT
log-status | OK

## まとめ

今回の実験を通して、以下のことがわかりました。

- VPC FlowLogsはAggregation Interval内に通過したパケットのカウンターのような挙動をとる
- VPC FlowLogsは、Aggregation Interval内に何パケット流れようとも1レコードとして記録される
- 記録される時間は、実際にパケットが通過した時間と最大60秒のラグがある

タイムラグがあるというところで、トラブルシュート時やSIEM等での分析時には少々注意が必要そうです。

以上