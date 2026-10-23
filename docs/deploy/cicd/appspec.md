---
title: 4.アプリケーション仕様ファイル(appspec)
last_modified_date: "2024-10-03"
nav_order: 4
parent: CICDテンプレート利用ガイド
---

# CICDテンプレート利用ガイド

## 4.アプリケーション仕様ファイル(appspec.yml)

### 4-1．appspec.ymlの作成

1. Blue/Greenを選択した場合  

```yml
version: 1
Resources:
- TargetService:
    Type: AWS::ECS::Service
    Properties:
        TaskDefinition: "Placeholder: GitHub Actions will fill this in"
        LoadBalancerInfo: 
            ContainerName: "tomcat"
            ContainerPort: 31100
```

ContainerNameには、タスク定義のコンテナ名を記述してください。  
この例では<span style="color: red; ">tomcat</span>です。  

### 4-2．githubに配置
作成したappspec.ymlをgithubにPushします(1-1※5)


<br>

<!--
<p style="margin-top: 20em"></p>  
-->
<div style="display: flex; justify-content: space-between;">
  <div style="text-align: center;">
  </div>
  <div style="text-align: center;">
　　<a href="./index">1.CICDテンプレート適用の流れ→</a>
  </div>
</div>


