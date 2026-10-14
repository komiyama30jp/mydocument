---
title: AI Gatewayテンプレート
last_modified_date: "2026-07-24"
parent: DCフレームワーク
nav_order: 4
---

## 目的  
担当者がDCフレームワークのAI Gatewayを構築するにあたり必要な要素をパラメータの入力のみでインフラ構築できるテンプレートを提供します。  


## 概要図
![CFnテンプレート作成イメージ](files/dcfw-ai-gateway-infra.svg)


## 提供テンプレート(CloudFormation)  

| ファイル名 | 用途 | 保存先 <br>(S3 URL) |  
|-----------------|------|--------------------|  
| [dcfw-ai-gateway-infra-template.yml](https://ap-northeast-1.console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/quickcreate?templateURL=https://dgcp-container-guide-311141522354.s3.ap-northeast-1.amazonaws.com/deploy/infrastructure/files/template/dcfw-ai-gateway-infra-template.yml){: target="_blank"} | DCFW Webテンプレート | `https://dgcp-container-guide-311141522354.s3.ap-northeast-1.amazonaws.com/deploy/infrastructure/files/template/dcfw-ai-gateway-infra-template.yml` |

{: .note}  
DGCPのAWSへログインした状態でymlリンクを開き、以下パラメータを入力/変更してください。  

## 入力パラメータ
### Project Configuration

| パラメータ名 | 必須項目 | デフォルト | 説明 |  
| --- | --- | --- | --- |
| SystemId | 〇 | - | 実行するシステム識別子を入力 |  
| EnvType | 〇 | - | [環境種別](/faq/dgcp_environment.html){:target="_blank"}を選択 |  
| BillingID | 〇 | - | 請求IDを入力 | 
| SecurityGroupIds | 〇 | - | [セキュリティグループ](/flow/#req3){:target="_blank"}を選択 |   

### ELB Configuration

| パラメータ名 | 必須項目 | デフォルト | 説明 |  
| --- | --- | --- | --- |
| ACM | 〇 | - | [ACM証明書](https://ap-northeast-1.console.aws.amazon.com/acm/home?region=ap-northeast-1#/certificates/list){:target="_blank"}のARNを入力<br> |  
| HealthCheckPath | - | /actuator/health | Springコンテナのヘルスチェックパスをを入力 |  
| Stickiness | - | true | Sticky Sessionの利用有無を入力 |  
| IdleTimeoutSeconds | - | 360 | アイドル接続状態解除までの時間（秒）を入力 |  

{: .tip}  
ACM証明書は、事前に[コンテナ利用フロー > 非本番環境の構築 > ACM証明書の発行](/flow/#req4){:target="_blank"}を参照し、ACM証明書の発行とDNS申請を行ってください。  
DGCP初期構築として払い出される構築検証用のACM`<AWSアカウントエイリアス名>.dentsu.co.jp`を利用することも出来ます。  


### ECS Configuration  

| パラメータ名 | 必須項目 | デフォルト | 説明 |  
| --- | --- | --- | --- |  
| ServiceNameSuffix | 〇 | ai-gateway | AI Gatewayのサービス名Suffixを入力 |  
| ECSClusterArn | - | - | 構築済みのECS Cluster ARNを入力（未入力の場合はCluster作成） |
| ContainerInsights | - | enabled | Clusterが作成された場合のContainer Insightの設定値を入力 |
| ECSContainerPort | - | 8080 | Webappの待ち受けポートを入力 |  
| ECSTaskCPUUnit | - | 1024 | タスクのCPUユニット数を入力 |  
| ECSTaskMemory | - | 2048 | タスクのメモリ容量を入力 |  
| ECSDesiredCount | - | 1 | 維持したいタスク数を入力 |  

### Logs Configuration

| パラメータ名 | 必須項目 | デフォルト | 説明 |  
| --- | --- | --- | --- |
| LogsRetentionInDays | - | 90 | ログの保持期間を入力 |  

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
| ALB | alb-t-ccntnt-int-ai-gateway |  
| Target Group | alb-tg-t-ccntnt-int-ai-gateway |  
| タスク実行ロール | rol-t-ccntnt-taskexecute-ai-gateway |  
| ECSクラスタ | ecs-cluster-t-ccntnt-ai-gateway |  
| ECSサービス | ecs-service-t-ccntnt-ai-gateway |  
| タスク定義 | ecs-task-t-ccntnt-ai-gateway |  
| タスクロール | rol-t-ccntnt-task-ai-gateway |  
| タスクロールポリシー | pol-t-ccntnt-task-ai-gateway |  
| CloudWatchLogs | /ecs/ecs-task-t-ccntnt-ai-gateway |  

{: .note}  
出力例は、下記の入力パラメータを設定した場合に出力される値です。
  
| パラメータ名 | 値 |  
| --- | --- |
| SystemId | ccntnt |  
| EnvType | t |  


## その他必要な作業について

1. アプリのコンテナイメージを作成する場合　→　[ベースイメージでのイメージビルド](/docker/build.html){:target="_blank"}  
1. ECR（Dockerイメージ格納用リポジトリ）を作成する場合　→　[手動デプロイガイド > ECRの構築手順](/deploy/manual/ecr-construction.html){:target="_blank"} or [インフラテンプレート > ECRテンプレート](/deploy/infrastructure/ecr.html){:target="_blank"}  
1. CICDを構成する場合　→　[CICDテンプレート利用ガイド](/deploy/cicd/){:target="_blank"}  
  
を参照してください。