---
title: トラブルシュート
last_modified_date: "2025-06-06"
nav_order: 6
parent: Fluent Bit（DGCP標準）利用ガイド
---

# Fluent Bit（DGCP標準）利用ガイド

## トラブルシュート

| 現象 | 原因 | 対応 |
| ------ | ------ | ------ |
| Firelensコンテナが起動しない | タスク定義の環境ファイルの`S3 ARN`が間違っている | タスクの停止理由に<br>`Resourceinitializationerror: failed to download env files`<br>と出力されている場合<br>[3.4ログコンテナ環境ファイルの追加](/fluentbit/firelens.html#3-4firelensコンテナに環境ファイルを追加)を参照し、<br>環境ファイルが正しく設定できているか確認してください。 |
| Firelensコンテナが起動しない | AWS管理コンソール上での入力時に改行コードや制御コードが誤って挿入されている。 | タスク定義の`Json形式`で「`？`」が入っていないか確認し、<br>入っている場合は削除してください。 |
| 設定ファイルが`S3`から取得できない。 | タスク定義に環境変数`CONF_BUCKET_DIR`が設定されていないまたは、誤った文字列が設定されている。 | firelensのログを確認し、<br>`--------apl_xxxxxx_xx.conf doesn't exist.--------`<br>が出力されている場合<br>環境変数が正しく設定できているか確認してください。<br>両端に誤って空白が挿入されることがあります。取り除いてください。 |
| Fluent Bitのバージョンアップ方法 | Fluent Bitの旧バージョン（2.x系）のバージョンアップの方法について | [5. Fluent Bit 3.x系へのバージョンアップ](/deploy/fluentbit/mig-configuration.html)を参照し、<br>バージョンアップを実施してください。 |
| ログストリーム名にecs-task-idが付かない | CloudWatch Logsに出力されるログのストリーム名にecs-task-idが付いていない場合 | fluentbitのBaseImageが旧バージョンのままとなっていないか確認してください。<br>[5.3 Fluent Bit 3.x系へのバージョンアップ-BaseImageの更新](/deploy/fluentbit/mig-configuration.html#5-3BaseImageの更新)を参照し、<br>新バージョンのBaseImageを使用してください。 |


<br><br>

<p style="margin-top: 20em"></p>  

<div style="display: flex; justify-content: space-between;">
  <div style="text-align: center;">
    <a href="/deploy/fluentbit/firelens">←5.Fluent Bit（DGCP標準）新バージョンへの移行について</a>
  </div>
  <div style="text-align: center;">
　
  </div>
</div>