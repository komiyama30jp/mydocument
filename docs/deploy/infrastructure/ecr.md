---
title: ECRテンプレート
last_modified_date: "2025-10-02"
parent: インフラテンプレート利用ガイド
nav_order: 3
---

## 目的
[デプロイ戦略](/deploy/strategy/){: target="_blank"}に基づき、ECRの配置も決定されますが、  
その際、`Cross-Account-Access（非本番→本番、本番→非本番の参照）`を許可する設定が必要であったり、イメージの世代管理を考慮する必要があります。  
従って、考慮するべきパラメータをテンプレートとして入力できる形として提供します。

## 概要図
<br>
  
![テンプレート概要](./files/ecr.svg)
<br>


## 提供テンプレート(CloudFormation)

| ファイル名 | 用途 | 保存先<br>(S3 URL) | 
| --- | --- | --- |
| [ecr-template.yml](https://ap-northeast-1.console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/quickcreate?templateURL=https://dgcp-container-guide-311141522354.s3.ap-northeast-1.amazonaws.com/deploy/infrastructure/files/template/ecr-template.yml){: target="_blank"} | ECRテンプレート | `https://dgcp-container-guide-311141522354.s3.ap-northeast-1.amazonaws.com/deploy/infrastructure/files/template/ecr-template.yml` |

{: .note}  
DGCPのAWSへログインした状態でymlリンクを開き、以下パラメータを入力/変更してください。  

## 入力パラメータ
### Project Configuration

| パラメータ名 | 必須項目 | デフォルト | 説明 |  
| --- | --- | --- | --- |
| SystemId | 〇 | - | 実行するシステム識別子を入力 |  
| EnvType | 〇 | - | [環境種別](/faq/dgcp_environment.html){:target="_blank"}を選択<br> |  
| BillingID | 〇 | - | 請求IDを入力 | 

### ECR Configuration

| パラメータ名 | 必須項目 | デフォルト | 説明 |  
| --- | --- | --- | --- |  
| RepositoryNameSuffix | 〇 | - | ECRリポジトリ名のSuffixを入力 |  
| Mutability | - | IMMUTABLE | イメージを上書きPushの可否設定。<br>`Immutable(上書き不可)`でないとDGCP側から確認されることがある。 |  
| GenerationManagement | - |  | ECRリポジトリ内でのイメージ世代管理数を入力 |  
| CrossAccountAccessAccountId1 | - | - | `Cross-Account-Access`を許可するAWSアカウントのIDを入力（１つめ） |  
| CrossAccountAccessAccountId2 | - | - | `Cross-Account-Access`を許可するAWSアカウントのIDを入力（２つめ） |  

### KMS Configuration

| パラメータ名 | 必須項目 | デフォルト | 説明 |  
| --- | --- | --- | --- |
| KMSKeyArn | - | - | KMSKeyのARNを入力 |  

{: .tip}  
・DGCPのKMSキー`<AWSアカウントID>-kms`  
・各担当者でKMSキー`要作成`  
どちらかのKMSキーで暗号化してください。  

{: .important}  
KMS暗号化は、情報区分「`極秘・取り扱い厳重注意`」時のみ必須となります。  


## 出力リソース

| リソースの種類 | 出力例 |
| --- | --- |
| ECR Repository | ecr-pri-`環境種別`-`システム識別`-`RepositoryNameSuffix` |  


## イメージの世代管理について  

ECRの保管料金は、非常に安価です。  

>プライベートリポジトリまたはパブリックリポジトリに保存されたデータのストレージは、GB/月あたり USD 0.10 です。 (AWS公式より引用)

```  
非本番： 26世代　（開発などでより多くのイメージをPushするため）  
本番　： 13世代  
```

など、アプリの更新頻度に合わせてパラメータ`GenerationManagement`を入力することを推奨します。  