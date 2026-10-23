---
title: タスク定義頻出パラメータ
last_modified_date: "2025-06-20"
parent: デプロイ関連
nav_order: 99
---


## タスク定義頻出パラメータ
以下では、重要もしくは頻繁に変更するパラメータに関して抜粋し、記述します。  
`タスク定義`で設定可能なパラメータは、[AWSリファレンス](https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/task_definition_parameters.html  
){:target="_blank"}を参照してください。  

- `family`  
    タスク定義のグループ。  
    ファミリーに対してリビジョンをバージョンとして連番で付与し、管理されます。  
    ```yaml
    family:  ecs-task-t-iappoc-web
    ```

- `requireCompatibilities`  
    Amazon ECS がタスク定義の検証基準となる起動タイプを指定できます。  
    `EC2 | FARGATE | EXTERNAL`から選択可。  
    ```yaml
    requiresCompatibilities:  ["FARGATE"]
    ```

- `taskRoleArn`  
    動作中のコンテナが利用するIAMポリシーを含んだロール。  
    ```yaml
    taskRoleArn:  arn:aws:iam::084563037095:role/rol-t-iappoc-taskrole-web
    ```

- `executionRoleArn`  
    タスクが起動する時に利用する（ECRからのイメージのPull, 環境変数の取得etc...）ロール。  
    ```yaml
    executionRoleArn:  arn:aws:iam::084563037095:role/rol-t-iappoc-taskexecuterole-web
    ```

- `networkMode`  
    タスクのコンテナで使用する Docker ネットワーキングモード。  
    Fargateは`awsvpc`のみ利用できます。  
    ```yaml
    networkMode:  awsvpc
    ```

- `cpu`  
    タスクに適用される CPU ユニットのハード制限。Fargateでは必須。  
    メモリと合わせて使用できる範囲が制限されます。後述の`ContainerDefinitions(コンテナ定義）`の総量よりも多い必要があります。  
    ```yaml
    cpu:  2048
    ```

- `memory`  
    タスクに適用されるメモリのハード制限。Fargateでは必須。  
    CPUと合わせて使用できる範囲が制限されます。`ContainerDefinitions(コンテナ定義）`の総量よりも多い必要があります。  
    ```yaml
    memory:  4096
    ```

    {: .tip}  
    [コンピューティングリソースの考え方](/container/resource/)を参照

- `containerDefinitions`  
    コンテナの設定を定義します。  
    - `name`  
        コンテナの名前。  
        ```yaml
        name:  ecs-container-t-iappoc-nginx
        ```
        - `image`  
        ECRにアップロードしているイメージ（タグまで含む）を指定します。  
        ```yaml
        image:  084563037095.dkr.ecr.ap-northeast-1.amazonaws.com/ecr-pri-t-iappoc-web:v1.0.0
        ```
    
    - `cpu`  
        Amazon ECS コンテナエージェントがコンテナ用に予約した cpu ユニットの数。  
        ```yaml
        cpu:  0
        ```
    
    - `memory`  
        コンテナに適用されるメモリの量 (MiB 単位)。  
        コンテナ内で動作しているアプリケーションが設定値を超えるとタスクが強制停止されます。  
        ```yaml
        memory:  3586
        ```

        {: .tip}  
        [コンピューティングリソースの考え方](/container/resource/)を参照
    
    - `memoryReservation`  
            コンテナ用に予約するメモリのソフト制限 (MiB 単位)。  
        予約したメモリのため、超えてもタスクは強制停止されません。  
        ```yaml
        memoryReservation: 0
        ```
    
    - `essential`  
        trueの場合、そのコンテナが何らかの理由で失敗または停止すると、タスクに含まれる他のすべてのコンテナは停止します。  
        ```yaml
        essential:  true
        ```
    
    - `stopTimeout`  
        コンテナが正常に終了しなかった場合にコンテナが強制終了されるまでの待機時間 (秒)。  
        APS標準では120秒。  
        ```yaml
        stopTimeout:  120
        ```
    
    - `portMapping`  
        コンテナはホストコンテナインスタンス上のポートにアクセスしてトラフィックを送受信するための設定。  
        awsvpcの場合、`containerPort` `protocol`が有効となり、`hostPort`は設定しないか、同じ値を設定します。  
        ```yaml
        portMappings:
            - containerPort: 31000
              hostPort: 31000
              protocol: tcp
        ```
    
    - `environment`  
        コンテナに渡す環境変数の設定。  
        Nameとvalueで宣言できます。  
        ```yaml
        environment:
            - name: JAVA_OPTS
              value: "-XX:InitialRAMPercentage=80.0 -XX:MaxRAMPercentage=80.0"
        ```
    
    - `environmentFile`  
        コンテナに渡す環境変数を含むファイルのリスト。  
        typeはs3のみサポートされ、valueはS3 ARNを設定します。  
        ```yaml
        environmentFiles:
            - value: arn:aws:s3:::s3-t-cinfrs-fsv-common-file/ccntnr/dgcp_t_ccntnr.env
              type: s3
        ```
    
    - `secrets`  
        コンテナに渡す機密情報系の環境変数の設定。  
        nameに環境変数名、valueFromに`Systems Manager Parameter Storeの名前又はArn`, `SecretsManagerのArn`を設定します。  
        ユースケースとしてはDBのユーザ、パスワードなどが該当します。  
        Parameter Storeの設定方法は[こちら](../parameterstore/)。
        ```yaml
        secrets:
            - name: SECRET_INFO
              valueFrom: /iappoc/t/SECRET_INFO
        ```
    
    - `logConfiguration`  
        コンテナのログ設定の仕様。  
        awslogsで直接CloudWatchLogsに送信するか、firelens経由でログ出力する等が設定できます。   
        詳細は、[Fluent Bit（DGCP標準）利用ガイド](/deploy/fluentbit/)を参照。  
        ```yaml
        logConfiguration:
            logDriver: awslogs
            options:
                awslogs-group: "/dgcp/apl/t-irognz/ecs-task-t-iappoc-web"
                awslogs-region: ap-northeast-1
                awslogs-stream-prefix: tomcat
        ```