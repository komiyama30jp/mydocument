---
title: 1.要対応項目
last_modified_date: "2025-06-06"
parent: FluentBit-2.x版
nav_order: 1
---

# Fluent Bit（DGCP標準）利用ガイド-2.x版

## 1.要対応項目

<details open markdown="block">
  <summary>
    目次
  </summary>
  {: .text-delta }

  - TOC
  {:toc}
</details>

---

### 1-1．設定ファイル配置場所の決定  
下記から方式を選択してください。  
<br>

1. **S3に配置（推奨）**  
    アプリ用の設定ファイルを`S3`に配置し、`Fluent Bit`が起動時に読み込む方式。  
    設定ファイル配置用の`S3バケット`を準備してください。`1.2の出力先用のバケット`と併用出来ます。  
<br>

1. **カスタムイメージを作成し、ローカルファイルシステムに配置**  
    アプリ用の設定ファイルをあらかじめ追加した、カスタムイメージを作成する方式。  
    `CloudWatch Logs`, `S3`, `NewRelic`以外に送信する場合は、強制的に本方式となります。

    {: .warning}  
    本方式の場合、サポートは行えません。

<br><br>

### 1-2．ログ出力先の決定
出力先として`CloudWatch Logs`、`S3`、`NewRelic`に対応しています。  
デフォルトでは、`CloudWatch Logs`に標準ミドルウェアのログを出力します。

{: .note}  
環境変数によってON/OFF可能  

<br>

それ以外の出力先は、下記ユースケースを元に、要件に応じて決定してください。  
<br>

1. **アプリログを長期保管したい**  
    `CloudWatch Logs`, `S3バケット`を準備してください。  
    <br>

1. **アプリのログを監視したい**  
    `CloudWatch Logs`, `NewRelic`の`ライセンスキー`を準備（※「監視設定依頼シート」を申請）してください。  
    
    {: .caution}
    <span style="color: red; ">本番環境</span>のみ必要となります。

    <br>

1. **アプリログを長期保管、監視したい**  
    `CloudWatch Logs`, `S3バケット`, `NewRelic`の`ライセンスキー`を準備してください。  
    <br>

<br><br>

### 1-3．Fluent Bitの設定ファイル作成、配置  
`1.1でS3を選択`した場合、「[2.設定ファイルと出力シート](/deploy/fluentbit/configuration)」を元に、設定ファイルを作成し、設定ファイル配置用のS3に配置してください。  
また、カスタムイメージ作成を選択した場合、`/fluent-bit/`に配置して下さい。  


<br><br>

<p style="margin-top: 20em"></p>  

<div style="display: flex; justify-content: space-between;">
  <div style="text-align: center;">
  </div>
  <div style="text-align: center;">
　　<a href="./configuration">2.設定ファイルと出力シート→</a>
  </div>
</div>