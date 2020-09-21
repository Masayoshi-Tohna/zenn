---
title: "対障害用語の一覧表"
emoji: "💪"
type: "idea"
topics: ["reliability", "fault", "fail"]
published: false
---

▼ 対障害用語の一覧表

<table>
	<tr>
		<td>#</td>
		<td>名称</td>
		<td>英語表記</td>
		<td>目的</td>
		<td>思想</td>
		<td>障害発生時の動作</td>
		<td>例</td>
	</tr>
	<tr>
		<td rowspan="2">1</td>
		<td>フォールトトレラント</td>
		<td>fault tolerant</td>
		<td rowspan="5">fault（システム障害）に備える</td>
		<td rowspan="2">システム障害が起きてもこれまで通りにしよう</td>
		<td rowspan="2">機能縮小せずに処理を継続する</td>
		<td>停電しても自家発電で給電を継続する</td>
	</tr>
	<tr>
		<td>フォールトトレランス</td>
		<td>fault tolerance</td>
		<td>RAIDを組むことでディスクが壊れても他のディスクで処理を継続する</td>
	</tr>
	<tr>
		<td rowspan="2">2</td>
		<td rowspan="2">フォールトアボイダンス</td>
		<td rowspan="2">faulr avoidance</td>
		<td rowspan="2">そもそもシステム障害が起こらないようにしよう</td>
		<td rowspan="2">事前対策によりシステム障害を回避する</td>
		<td>高品質な部品を使って障害発生可能性を極力低くする</td>
	</tr>
	<tr>
		<td>徹底的にテストを行って障害発生可能性を極力低くする</td>
	</tr>
	<tr>
		<td>3</td>
		<td>フォールトマスキング</td>
		<td>fault masking</td>
		<td>システム障害が起きても他に影響しないようにしよう</td>
		<td>正しい処理結果のみ採用する</td>
		<td>複数のCPUで同じ処理を行い異なる結果（少数派）は排除する</td>
	</tr>
	<tr>
		<td>4</td>
		<td>フェールセーフ</td>
		<td>fail safe</td>
		<td rowspan="5">fail（異常）に備える</td>
		<td>異常が起きたら停止しよう</td>
		<td>異常を検知したら停止する</td>
		<td>異常な入力に対して警告を表示し処理を停止する</td>
	</tr>
	<tr>
		<td>5</td>
		<td>フェールソフト</td>
		<td>fail soft</td>
		<td>異常が起きても柔軟に対処しよう</td>
		<td>異常を検知したら縮退運転する</td>
		<td>2台のサーバで賄っていた処理を1台で継続する</td>
	</tr>
	<tr>
		<td>6</td>
		<td>フェールオーバー</td>
		<td>failover</td>
		<td>異常が起きたら待機系に引き継ごう</td>
		<td>異常を検知したら待機系に切り替える</td>
		<td>主系のサーバがダウンしても副系のサーバでサービス継続する</td>
	</tr>
	<tr>
		<td>7</td>
		<td>フェールバック</td>
		<td>failback</td>
		<td>異常が復旧したら主系に引き継ごう</td>
		<td>復旧したら主系に切り替える</td>
		<td>主系が復旧したら副系から主系に処理を戻す</td>
	</tr>
	<tr>
		<td>8</td>
		<td>フォールバック</td>
		<td>fallback</td>
		<td>異常が起きる前に対処しよう</td>
		<td>異常が発生する前に縮退運転に切り替える</td>
		<td>回線速度に応じてYouTubeの画質を落とす</td>
	</tr>
	<tr>
		<td>9</td>
		<td>フールプルーフ</td>
		<td>foolproof</td>
		<td>バカに備える</td>
		<td>想定外の使い方に対処しよう</td>
		<td>想定外が発生しないようにする</td>
		<td>電話番号入力フォームには数字しか入力できないようにする（バリデーション）</td>
	</tr>
</table>
