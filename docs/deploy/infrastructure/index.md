---
title: インフラテンプレート利用ガイド
last_modified_date: "2025-07-30"
parent: デプロイ関連
nav_order: 2
---

# インフラテンプレート利用ガイド  

## 提供するテンプレートについて
各種、必要なAWSリソースが含まれています。  
テンプレートを利用する際は、各ページに記載している`AWS管理コンソール（CloudFormation）のリンク`から開いて実行するか`S3 URL(SNET限)`または[GitHub](https://github.com/dentsusoken/dgcp-container-guide){: target="blank"}からファイルを取得、実行してください。  

{: .note}  
テンプレートの修正等の通知を行う予定はございません。  
ファイルを別途取得した際は、ご留意ください。  

{: .important}  
テンプレートファイルに対するカスタマイズは、自己責任にて行なってください。  
※パラメータの内容や変更などの問い合わせは可能です。  
<br>
追加リソースなどは手動での構築も検討してください。  

## よくある質問
<u> Q1. テンプレートで構築した場合は、構成管理もテンプレートで行った方がよい？ </u>  
各アプリチームのスキルセットに応じてご判断ください。  
初期構築は、テンプレートを利用して実施。  
後のパラメータ以外の変更やリソース追加は手動で更新を想定しています。  
  
<u> Q2. 手動で変更した場合は、管理できなくなる？ </u>  
テンプレート上のパラメータをAWS管理コンソール上の手動で変更した場合でも`ドリフトの検知`でテンプレートとの差異を評価することが出来ます。  
また、AWSリソースの構成管理としては、[AWS Config](/operation/forensic/){: target="_blank"}というサービスで記録されています。  
従ってパラメータ等の変更があったか、前の設定は何だったかはテンプレートに限らず行うことが出来るため、  
各アプリチームのスキルセットに応じてご判断いただくのが運用上望ましいものと考えています。  
  
<u> Q3. TerraformやAWS CDKを利用してはいけない？ </u>  
問題ありません。　※サポート対象外  
テンプレートとして`CloudFormation`を採用しているのは、「実行環境」「統一言語」「AWSサポートの利用可否」の観点から採用しています。  

下記を参考に移行してください。  
[Terraformへの移行](https://tech.nri-net.com/entry/migrating_from_cloudformation_management_to_terraform_management){:target="_blank"}  
[CDKへの移行](https://aws.amazon.com/jp/blogs/news/migrating-cloudformation-templates-to-the-aws-cloud-development-kit/){:target="_blank"}  

<u> Q4. テンプレート(yml)ファイルを取得できない（403となる） </u>  
ファイルは、セキュリティを考慮したバケットポリシーを構成したS3上にデプロイされています。  

DGCPのAWSアカウント上の権限を持ちアクセスするか（[管理コンソールアクセス](https://console.aws.amazon.com/s3/buckets/dgcp-container-guide-311141522354?region=ap-northeast-1&tab=objects&prefix=deploy/infrastructure/files/template/&bucketType=general){: target="_blank"}または`AWS CLI`など）  
AWSの認証情報なしhttpsアクセスの場合は、 **SNET** からのみです。  