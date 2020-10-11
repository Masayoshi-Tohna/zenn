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

<table>
	<tr>
		<td>timestamp</td>
		<td>1602397806</td>
	</tr>
	<tr>
		<td>*timestamp</td>
		<td>2020/10/11(日) 15:30:06</td>
	</tr>
	<tr>
		<td>version</td>
		<td>2</td>
	</tr>
	<tr>
		<td>account-id</td>
		<td>xxxxxxxxxxxx</td>
	</tr>
	<tr>
		<td>interface-id</td>
		<td>eni-0ff9b2e1f8edb8688</td>
	</tr>
	<tr>
		<td>srcaddr</td>
		<td>xxx.xxx.xxx.xxx</td>
	</tr>
	<tr>
		<td>dstaddr</td>
		<td>10.255.1.22</td>
	</tr>
	<tr>
		<td>srcport</td>
		<td>52233</td>
	</tr>
	<tr>
		<td>dstport</td>
		<td>22</td>
	</tr>
	<tr>
		<td>protocol</td>
		<td>6</td>
	</tr>
	<tr>
		<td>packets</td>
		<td><b>85</b></td>
	</tr>
	<tr>
		<td>bytes</td>
		<td>8216</td>
	</tr>
	<tr>
		<td>start(unixtime)</td>
		<td>1602397806</td>
	</tr>
	<tr>
		<td>*start</td>
		<td>2020/10/11(日) 15:30:06</td>
	</tr>
	<tr>
		<td>end(unixtime)</td>
		<td>1602397865</td>
	</tr>
	<tr>
		<td>*end</td>
		<td>2020/10/11(日) 15:31:05</td>
	</tr>
	<tr>
		<td>*経過時間</td>
		<td><b>0:00:59</b></td>
	</tr>
	<tr>
		<td>action</td>
		<td>ACCEPT</td>
	</tr>
	<tr>
		<td>log-status</td>
		<td>OK</td>
	</tr>
</table>

一方で、1パケットしか通過しなくても1レコードが59秒（=約1分）のレコードも存在しました。これが、ドキュメントに記載されている「パケットがネットワークインターフェイス上で送信または受信されてから最大60秒になる場合がある」という事象のようです。

<table>
	<tr>
		<td>timestamp</td>
		<td>1602397446</td>
	</tr>
	<tr>
		<td>*timestamp</td>
		<td>2020/10/11(日) 15:24:06</td>
	</tr>
	<tr>
		<td>version</td>
		<td>2</td>
	</tr>
	<tr>
		<td>account-id</td>
		<td>xxxxxxxxxxxx</td>
	</tr>
	<tr>
		<td>interface-id</td>
		<td>eni-0ff9b2e1f8edb8688</td>
	</tr>
	<tr>
		<td>srcaddr</td>
		<td>xxx.xxx.xxx.xxx</td>
	</tr>
	<tr>
		<td>dstaddr</td>
		<td>10.255.1.22</td>
	</tr>
	<tr>
		<td>srcport</td>
		<td>52233</td>
	</tr>
	<tr>
		<td>dstport</td>
		<td>22</td>
	</tr>
	<tr>
		<td>protocol</td>
		<td>6</td>
	</tr>
	<tr>
		<td>packets</td>
		<td><b>1</b></td>
	</tr>
	<tr>
		<td>bytes</td>
		<td>136</td>
	</tr>
	<tr>
		<td>start(unixtime)</td>
		<td>1602397446</td>
	</tr>
	<tr>
		<td>*start</td>
		<td>2020/10/11(日) 15:24:06</td>
	</tr>
	<tr>
		<td>end(unixtime)</td>
		<td>1602397505</td>
	</tr>
	<tr>
		<td>*end</td>
		<td>2020/10/11(日) 15:25:05</td>
	</tr>
	<tr>
		<td>*経過時間</td>
		<td>0:00:59</td>
	</tr>
	<tr>
		<td>action</td>
		<td>ACCEPT</td>
	</tr>
	<tr>
		<td>log-status</td>
		<td>OK</td>
	</tr>
</table>

「最大60秒」と言っていることからわかるように、以下の例では経過時間が39秒となっており、必ずしも経過時間が1分となるわけではないようです。

<table>
	<tr>
		<td>timestamp</td>
		<td>1602397706</td>
	</tr>
	<tr>
		<td>*timestamp</td>
		<td>2020/10/11(日) 15:28:26</td>
	</tr>
	<tr>
		<td>version</td>
		<td>2</td>
	</tr>
	<tr>
		<td>account-id</td>
		<td>xxxxxxxxxxxx</td>
	</tr>
	<tr>
		<td>interface-id</td>
		<td>eni-0ff9b2e1f8edb8688</td>
	</tr>
	<tr>
		<td>srcaddr</td>
		<td>xxx.xxx.xxx.xxx</td>
	</tr>
	<tr>
		<td>dstaddr</td>
		<td>10.255.1.22</td>
	</tr>
	<tr>
		<td>srcport</td>
		<td>52233</td>
	</tr>
	<tr>
		<td>dstport</td>
		<td>22</td>
	</tr>
	<tr>
		<td>protocol</td>
		<td>6</td>
	</tr>
	<tr>
		<td>packets</td>
		<td><b>1</b></td>
	</tr>
	<tr>
		<td>bytes</td>
		<td>136</td>
	</tr>
	<tr>
		<td>start(unixtime)</td>
		<td>1602397706</td>
	</tr>
	<tr>
		<td>*start</td>
		<td>2020/10/11(日) 15:28:26</td>
	</tr>
	<tr>
		<td>end(unixtime)</td>
		<td>1602397745</td>
	</tr>
	<tr>
		<td>*end</td>
		<td>2020/10/11(日) 15:29:05</td>
	</tr>
	<tr>
		<td>*経過時間</td>
		<td><b>0:00:39</b></td>
	</tr>
	<tr>
		<td>action</td>
		<td>ACCEPT</td>
	</tr>
	<tr>
		<td>log-status</td>
		<td>OK</td>
	</tr>
</table>

## まとめ

今回の実験を通して、以下のことがわかりました。

- VPC FlowLogsはAggregation Interval内に通過したパケットのカウンターのような挙動をとる
- VPC FlowLogsは、Aggregation Interval内に何パケット流れようとも1レコードとして記録される
- 記録される時間は、実際にパケットが通過した時間と最大60秒のラグがある

タイムラグがあるというところで、トラブルシュート時やSIEM等での分析時には少々注意が必要そうです。

以上