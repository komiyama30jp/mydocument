---
title: 1.CICDテンプレート適用の流れ
last_modified_date: "2024-10-03"
nav_order: 1
parent: CICDテンプレート利用ガイド
---

# CICDテンプレート利用ガイド

## 1.CICDテンプレート適用の流れ
### 1-1．本ドキュメントの例  
#### githubの階層  
このテンプレートで使用するgithubのファイル構造は下記の通りとします。  
このテンプレートでは、環境ごとに必要なファイルは、「_dev」としています。  
環境毎のファイルは適宜作成してください。

```plaintext
    root  
      +-Dockerfile              ※1
      +-pom.xml                 ※2
      |  
      +-.github/workflows  
      |  +-cicd_rolling_dev.yml    ローリングアップデート用のGitHubActiosのファイル
      |  +-cicd_bluegreen_dev.yml  BlueGreenデプロイ用のGitHubActiosのファイル
      +-poc2                    ※3アプリのプロジェクト
         +-.src  
         |  +...  
         +-.aws  
           +-taskdef_dev.json     ※4
           +-appspec_dev.yml      ※5
```     

### 1-2．デプロイ方式の決定  
Blue/Greenデプロイメントかローリングアップデートのどちらの方式を採用するか決定してください。  
デプロイ方式の詳細は[【ECS】コンテナデプロイ方式検討.xlsx ](https://esq365.sharepoint.com/:x:/s/msteams_30dfd0/Ee8N9W8uFx5PuPZCnjEEAQMB9B2EoO3yN95imlYjEaFy_g?e=tsRFZN)を参照してください。
<br>
1. ローリングアップデート(標準デプロイ方式)を選択した場合  
<br>
下記2つのファイルを環境別に準備します。

    ①   Dockerfile  
    ベースとなるイメージに対し、どのような操作をするかを記したファイル。  
    基本的な話なので、詳細割愛。  
<br>
    ➁   タスク定義  
    デプロイするコンテナイメージ、コンテナに割り当てるリソース、IAMロール、CloudWatchLogsの出力先等を指定する  
    定義ファイル。ファイル名はtaskdef_dev.jsonとして保存します。

1. Blue/Greenを選択した場合  
<br>
上記①②に加え下記を準備します。  
<br>
    ③   アプリケーション仕様ファイル(appspec_dev.yml)  
    どのサービスをデプロイするか、どの定義を元にどこのインフラへリリースするかを定義したファイル。  
    ファイル名はappspec_dev.ymlとします。
<br>


### 1-3．GitHubActionsの構成ファイルを作成
下記のフローに沿ってテンプレートを修正します。 詳細は次の章で説明します。 
1. ビルドツールを使用しWar(Jar)ファイル作成
1. AWS、ECRにログイン  
1. ECRにDockerImageをPush  
1. タスク定義書き換え  
1. タスク定義書置換、ECSデプロイ  

### 1-3．AWSにロールを作成
１. IAMロールを作成し、GitHubのリポジトリとの信頼関係を作成します。  

ロール名
```Role
ロール名：rol-[環境識別子]-[システム識別子]-githubactions
```  
信頼関係

```json
{
    "Version": "2008-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": "arn:aws:iam::[AWSアカウント]:oidc-provider/token.actions.githubusercontent.com"
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": {
                "StringLike": {
                    "token.actions.githubusercontent.com:sub": [
                        "repo:dentsusoken/[リポジトリ名]:*"
                    ]
                }
            }
        }
    ]
}
```  
 ２. 作成したロールに、以下のポリシーをアタッチしてください。
 
```yaml
- arn:aws:iam::aws:policy/AmazonECS_FullAccess
- arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryPowerUser
- arn:aws:iam::aws:policy/AWSCodePipeline_FullAccess
- arn:aws:iam::aws:policy/SecretsManagerReadWrite
```

```json
{
    "Statement": [
        {
            "Action": [
                "codedeploy:Get*",
                "codedeploy:Batch*",
                "codedeploy:List*",
                "codedeploy:CreateDeployment",
                "codedeploy:RegisterApplicationRevision",
                "iam:GetGroup*",
                "iam:List*",
                "iam:PassRole",
                "kms:Decrypt",
                "ssm:*ToResource",
                "ssm:*Parameter*"
            ],
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
```

{: .note}
業務系の旧基盤では、IAM申請書を元にIAMロールの作成、pol-dgcp-write-oidc-all、pol-dgcp-write-oidc-all_2を付与する申請となります。

{: .note}
業務系の新基盤では、CloudFormationのテンプレートを利用できます。  
[github-template.yml](https://dgcp-container-guide-311141522354.s3.ap-northeast-1.amazonaws.com/deploy/cicd/files/template/github-template.yml)  
を活用してください。

{: .warning}
動作確認のため、最小権限のポリシーにはなっておりません。予めご留意ください。

<br>



<!--
<p style="margin-top: 20em"></p>  
-->
<div style="display: flex; justify-content: space-between;">
  <div style="text-align: center;">
  </div>
  <div style="text-align: center;">
　　<a href="./actions">2.GitHubActions→</a>
  </div>
</div>




