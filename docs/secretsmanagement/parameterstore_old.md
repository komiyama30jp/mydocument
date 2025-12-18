---
title: Parameter Storeの利用(旧業務系)
last_modified_date: "2025-07-11"
parent: 機密情報の保管方式
nav_order: 4
---

## Parameter Storeの利用
`AWS`の`Systems Manager`の機能の1つで、機密管理のための安全な階層型ストレージを提供する。  
`ECS`では`タスク定義`の`コンテナ定義>Secrets`にて利用する。  
`パスワード`、`データベース文字列`、`Amazon Machine Image (AMI) ID`、`ライセンスコード`などのデータをパラメータ値として保存することができる。  

{: .note}
`Parameter Store`を利用するケースは、重要な認証情報を保管することが多いため、  
平文ではなく、`KMS暗号化`した状態で利用すること。  
<br>
※平文の場合は、ECSの`タスク定義`に記載すれば良い

<br>
    
```yaml
Name: '/iappoc/t/example' #パラメータストアの名前。/<システム識別子:6桁>/<任意の文字列>
Description: 'example parameter store' #パラメータストアの説明
Value: 'Sample' #実際に入れる値（文字列）
Type: SecureString #SecureStringを指定
KeyId: 'alias/kms-p-g-dgcp-dev-app-01-01' #非本番本番問わず記載済みのKMSキーを指定する。
Tags:
  - Key: 'Cost1'
    Value: 'iappoc' #システム識別子は変更する
  - Key: 'Cost2' 
    Value: 'Kaihatsu' #環境毎に変更「kaihatsu | kensho | iko | sogo | giji | honban 」
DataType: 'text'
```

<br>

2. ファイルを元にパラメータストアに値を格納する  
`aws ssm put-paramater --cli-input-yaml fileb://[保存したyamlファイルのパス]`  
![SSM例](./files/ssm-ex.png)  

## タスク定義  
### 新しいリビジョンの作成  
`新しいリビジョンの作成` > `JSONを使用した新しいリビジョンの作成` を押下します  

以下の内容を追加してください。  

```json  
"containerDefinitions": [
    {
      //～略～
        "secrets":[
          {
              "name" : "《環境変数名》",
              "valueFrom" : "《作成したParameter Storeの名前》"
          }
      ]
    }
]

//例
"containerDefinitions": [
    {
    //～略～ 
      "secrets": [
          {
              "name": "XXX_API_KEY",
              "valueFrom": "/ccntnt/prd/tomcat/XXX_API_KEY"
          }
      ]
    }
]
```